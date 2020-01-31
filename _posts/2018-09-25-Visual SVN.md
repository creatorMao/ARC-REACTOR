---
layout:     post
title:      安装Visual SVN
subtitle:   
date:       2018-09-25
author:     5只猫
header-img: img/article-bg.jpg
catalog: true
tags:
    - Visual SVN
---

# Oracle 数据库安装心得和一些遇到的问题

## 安装
* 官网下载安装包 [
Oracle Database 12c Release 2 (12.2.0.1.0)]([https://www.oracle.com/technetwork/database/enterprise-edition/downloads/oracle12c-windows-3633015.html?ssSourceSiteId=otncn)
* 安装包安装好后，自带SqlPlus(Dos命令行工具)和SqlDeveloper(可视化工具)，但推荐使用其它的工具PL/SQL Developer12
## 安装遇到的问题
我的服务器是学生机机，性能单核 1g内存，可怜的想哭.....我发现只要我在本地一用PL/SQL 远程连接数据库之后，服务器的cpu就一直爆掉，一直稳定在100%;整个服务器非常卡，而且我还没有执行任何的sql语句....
![cpu一直100%](/img/postimages/2.png)
上网查资料以后，网上说是PL/SQL的bug，修改配置即可。
![修改配置](/img/postimages/3.jpg)
用户界面->代码助手->描述前后关系  这里取消选择就行。具体这个描述前后关系有什么作用，暂时先不考虑了</br>
[plsql developer连接数据库导致服务器cpu升高的案例](https://blog.csdn.net/killvoon/article/details/78506096)

## 用户
* sys, system 这两个权限都比较高，sys需要使用管理员权限登录，而system可以直接登录。密码是安装的时候设置的密码。
* scott 默认密码是tiger(12c的版本已经没有这个账号了)
* 修改密码
```
alter user [username] identified by [newpassword]
```
* 查找所有用户
```
desc dba_users
select * from dba_users
```
* 解锁&锁用户
```
alter user [username] account unlock
alter user [username] account lock
```

## 登录
* 普通登录
```
[username/password] [@server] [as sysdba|sysoper]
```
  远程连接数据库 scott/tiger @xxxxx/orcl 
* sys账号需要使用管理员权限登陆:
```
  connect sys/password as sysdba
```
* 遇到的问题：
由于我的数据库是装在远程服务器里的，我在远程服务器的电脑里，在SqlPlus中用sys以及system都可以登录，但是在我本地的电脑中，使用SqlPlus登录却提示登录失败，提示登录禁止。
![这里是图片](/img/postimages/QQ截图20180918152305.jpg)
可能是处于安全的考虑，将这两个用户不允许远程登录，但是新创建的用户的确是可以远程登录的。
这个具体很面再看为什么...

## 表空间
 * 表空间是区别Oracle与其它数据库的一个重要区别。SqlServer中可以建很多个数据库，而在Oracle数据库当中，只有一个主数据库，数据库中有很多的表空间和用户，可以指定用户是哪个表空间。登录oracle数据库之后，会显示当前用户所在的表空间下的所有数据。
* 表空间包括：
* 永久表空间 (表、视图、存储过程等)
* 临时表空间 (中间过程，结束之后，会释放掉)
* UNDO表空间 (执行事务之前，将数据存放到这里，用于回滚)
* 在创建一个新的用户的时候，最好指定默认表空间，而不是使用默认的system表空间。（sys和system等系统用户采用的默认表空间是system表空间。）
网上查到默认是分配到system，我的12c版本默认是在USER表空间。

* 创建一个表空间
```
create tablespace [tablespaceName] datafile [filePath] size [size];

create tablespace run datafile 'c:\oracleData\run.dbf' size 2000M;
```

* 创建一个临时表空间
```
create temporary tablespace [tablespaceName] tempfile [filePath] size [size];

create temporary tablespace run_temp tempfile 'c:\oracleData\run_temp.dbf' size 2000M;
```
* 创建一个新用户，设置该用户的默认表空间,临时表空间
```
create user [userName]
  identified by [password]]
  default tablespace [default tablespace Name]
  temporary tablespace [temporary tablespace name]

create user c##mzw
    identified by 123456789
    default tablespace run
    temporary tablespace run_temp;
```
## 遇到的问题
* 12c版本中，用户名字需要前缀c##，不懂为什么....别的版本貌似是不需要。
* 创建好账号以后，直接登录报错。错误信息如下
ORA-01045: 用户 C##MZW 没有 CREATE SESSION 权限; 登录被拒绝
![账号报错](/img/postimages/4.jpg)
这个错误是当前用户没有登录权限，给用户赋予create session权限即可。
```
grant create session to c##mzw;
```
授权成功后，可以查看当前用户具有的权限
```
select * from session_privs;
```
不出所料，当前用户只有一个登录权限。
![当前用户权限](/img/postimages/6.jpg)

## 权限&角色
角色即权限的集合，可以把一个角色授予给用户(要不然一个个赋值权限实在是太累了)</br>

* 查看当前用户的角色
```
SELECT * FROM USER_ROLE_PRIVS;
```

* 查看系统中所有的角色
```
SELECT * FROM DBA_ROLE_PRIVS;
```
系统的所有角色有：</br>
* DBA (是授予系统管理员的，拥有该角色的用户就能成为系统管理员了，它拥有所有的系统权限。网上的教程都是直接赋予grant dba to user,毕竟直接拥有最高的管理权限方便很多，但是有安全性的问题)
* CONNECT (是授予最终用户的典型权利，最基本的权利，能够连接到ORACLE数据库中，并在对其他用户的表有访问权限时，做SELECT、UPDATE、INSERTT等操作。)
* RESOURCE (是授予开发人员的，能在自己的方案中创建表、序列、视图等。看不懂....)
* ....</br>

* 查看当前用户具有的权限
```
select * from session_privs;
```
* 系统中有的权限
* create session  //授予zhangsan用户创* session的权限，即登陆权限
*unlimited tablespace  //授予使用表空间的权限
* create table  //授予创建表的权限
* drop table  //授予删除表的权限
* insert table  //插入表的权限
* update table  //修改表的权限
* ....

## 总结
我目前的理解，在创建项目的时候，先创建一个新的表空间，临时表空间，用户。</br>
将用户的默认表空间和临时表空间设置好。</br>
再赋予用户不同的角色。角色可以直接设置或者使用系统提供的标准角色，当然也可以直接赋予权限。


## THANKS TO
* [Oracle创建表空间和表](https://www.cnblogs.com/qmfsun/p/3817344.html)
* [Oracle创建表空间、创建用户并指定该用户的表空间、授权](Oracle创建表空间、创建用户并指定该用户的表空间、授权)
* [oracle用户创建及权限设置](https://www.cnblogs.com/shlcn/archive/2011/07/21/2112879.html)
* [oracle查看当前用户权限](https://www.cnblogs.com/wuer888/p/7484765.html)
* [connect、resource和dba三种标准角色](http://www.cnblogs.com/jianshuai520/p/9620445.html)














