---
layout:     post
title:      Git
subtitle:   
date:       2020-01-31
author:     5只猫
header-img: img/article-bg.jpg
catalog: true
tags:
    - Git
---


# Git 简史

    Git一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。

![20200131190603.png](http://qny.smartcoder.club/bed/20200131190603.png)

# Git/GitHub的区别

- git

    git是一个版本管理工具，是可以在你电脑不联网的情况下，只在本地使用的一个版本管理工具，其作用就是可以让你更好的管理你的程序。

- github

    是一个大型的同性交友平台。(gayhub？？hhhh)

    github从名字上理解，它是一个git的集合。

    github是一个底层用了git这个工具的代码管理平台。

    每个人都可以在github上创建一个代码仓库，上传自己的代码。用来管理控制自己的代码版本。


# SVN/Git 区别

分类 | 代表性应用 
---|---
集中式版本控制工具| SVN
分布式版本控制工具 | Git

### 分类上的不同

- 集中式版本控制工具

    版本历史记录只保存在一台服务器上，如果服务器出现故障，版本控制就没了。

![20200131190621.png](http://qny.smartcoder.club/bed/20200131190621.png)


- 分布式版本控制工具

    每台客户端上都有完整的版本历史记录，避免服务器出现故障。

![20200131190637.png](http://qny.smartcoder.club/bed/20200131190637.png)


### 版本控制上的不同

- SVN 采用增量方式

- Git 文件快照(?)  

![20200131190700.png](http://qny.smartcoder.club/bed/20200131190700.png)

# Git 优势

- 大部分操作在本地完成，不需要联网 (分布式)

- 完整性保证 (Hash验证)

- 尽可能添加数据，而不是修改或者删除数据 (版本控制)

- 分支操作非常快捷流畅 (快照，指针)

- 与Linux命令全面兼容 (同一个爹)

# Git 结构

![20200131190714.png](http://qny.smartcoder.club/bed/20200131190714.png)

# Git与代码托管中心

代码托管中心的任务：维护远程库

代码托管中心种类：

- 本地环境
    - GitLab托管中心
- 外网环境
    - GitHub
    - 码云

# 本地库与远程库的交互

- 团队内协作

![20200131190730.png](http://qny.smartcoder.club/bed/20200131190730.png)

- 跨团队协作

![20200131190745.png](http://qny.smartcoder.club/bed/20200131190745.png)

# 本地库初始化

```
  git init
```

# 设置签名

- 项目/仓库级别：`仅在当前本地库有效`

```
  git config user.name mzw #设置用户名 mzw
  git config user.email zwmao@outlook.com #设置用户邮箱

  保存位置：~/.gitconfig 文件
```


- 系统用户级别：`仅在当前登录的操作系统用户有效`

```
  git config --global user.name mzw #设置用户名 mzw
  git config --global user.email zwmao@outlook.com #设置用户邮箱

  保存位置：C:\Users\毛致武\.gitconfig
```

> 优先级别：项目级别 > 系统级别

# 基本操作

### 1.1状态查看

```
# 查看工作区、暂存区状态
git status 
```

### 1.2添加

```
# 将工作区的文件添加到暂存区。
git add fileName  #指定文件
git add .         #所有           
```

### 1.3 提交到本地库

```
# 将暂存区内容提交到本地库
git commit -m 'commit message' fileName
```

### 1.4 查看历史版本记录

```
git log 
git reflog  #常用
git log --pretty=oneline #漂亮一行显示
git log --oneline #简洁显示
说明：HEAD@{移动到当前版本需要多少步}
```

### 1.5 回滚

- 基于索引值 `推荐`

  ```
  git reset --hard 指针位置
  例子：git reset --hard a6ace91 #回到这个状态
  ```

- 使用 **^** 符号 `只能后退`

  ```
  git reset --hard HEAD^
  例子：git reset --hard HEAD^^
  注意：几个 ^ 表示后退几步
  ```

- 使用 **~** 符号 `只能后退`

  ```
  git reset --hard HEAD~n
  例子：git reset --hard HEAD~3
  ```

### 1.6 reset的三个参数比较

```
soft: 
  - 仅本地库移动HEAD 指针
mixed:
  - 在本地库移动HEAD指针
  - 重置暂存区
hard:
  - 在本地库移动HEAD指针
  - 重置暂存区
  - 重置工作区
```


# Git 结合Github

#### 1.1 创建远程库地址别名

~~~
git remote -v  #查看远程地址别名
git remote add 别名 远程地址 
例子：git remote add origin https://xx
~~~

#### 1.2 推送

`开发修改完把本地库的文件推送到远程仓库` `前提是提交到了本地库才可以推送`

~~~
git push 别名 分支名
git push -u 别名 分支名    #-u指定默认主机
例子：git push origin master
~~~

push时，需要登录github账号

![20200131190810.png](http://qny.smartcoder.club/bed/20200131190810.png)

#### 1.3 克隆

`完整的把远程库克隆到本地`  `克隆下来后不要在主分支里面做开发` `clone进行一次，从无到有的过程，更新用pull`

~~~
git clone  远程地址
例子：git clone https://xx
~~~

#### 1.4 拉取

  `本地存在clone下来的文件  就用pull更新`  

```
pull = fetch + merge
	git fetch 别名 分支名
	git merge 别名 分支名
git pull 别名 分支名
```