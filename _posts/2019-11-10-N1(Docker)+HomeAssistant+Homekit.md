---
layout:     post
title:      N1(Docker)+HomeAssistant+Homekit
subtitle:   
date:       2019-11-10
author:     5只猫
header-img: img/article-bg.jpg
catalog: true
tags:
    - HomeAssistant
---

### 关系&介绍
- 米家

小米智能家庭App,可以控制小米智能设备和第三方智能设备。使用方便，但没有网页端控制。
- HomeAssistant
> Home Assistant 是一款基于 Python 的智能家居开源系统，支持众多品牌的智能家居设备，可以轻松实现设备的语音控制、自动化等。

目前做的比较好的智能设备控制平台。支持web端，可轻松移植到不同的应用里(微信小程序等)。
- Homekit
> 苹果2014年发布智能家居平台HomeKit。

ios里的家庭App，可以通过Siri语音控制设备。
但是支持homekit的设备大多数都太贵。

- HomeBridge 

一个插件，桥接不支持HomeKit的设备和HomeKit。因为homebridge的开发者是原来homekit开发团队的成员，所以逆向了homekit的协议，可以让不是homekit的设备与苹果接入，从而达到使用siri去控制智能设备的目的。

ps:新版本的HomeAssistant自带了homekit功能，所以目前其实用不到这个桥接插件了。

- Docker 

容器引擎，相当于虚拟机，可以运行打包好的系统。

![20200131230034.png](http://qny.smartcoder.club/bed/20200131230034.png)

### 原理
在linus系统下，安装Docker，同时安装HomeAssistant和HomeBridge容器，把设备都挂载到HomeAssistant平台上，HomeAssistant通过HomeBridge插件桥接到苹果的HomeKit，从而达到Siri可以控制设备的目的。

![20200131230054.png](http://qny.smartcoder.club/bed/20200131230054.png)

![20200131230110.png](http://qny.smartcoder.club/bed/20200131230110.png)

![20200131230127.png](http://qny.smartcoder.club/bed/20200131230127.png)

### 安装环境

##### 1. 安装Dcoker环境

我选用的是N1刷的荒野无灯的小钢炮自带Docker环境。

##### 2. 安装HomeAssistant

在Docker里安装对应版本的HomeAssistant。

ps:论坛里说小钢炮是aarch64架构的，所以安装的是aarch64-homeassistant。我也不懂...回过头来再学习。

[Docker镜像地址汇总地址](https://hub.docker.com/search?q=homeassistant&type=image)


```
docker run -d --name homeassistant --restart=always --net=host -v /var/lib/docker/2homeassistant/config:/config homeassistant/aarch64-homeassistant:latest

```

![20200131230145.png](http://qny.smartcoder.club/bed/20200131230145.png)

安装成功以后，如果你的Docker环境配置了图形化界面工具，可以登录查看。

![20200131230158.png](http://qny.smartcoder.club/bed/20200131230158.png)

使用WinSCP软件可以进入到刚才命令中映射的文件夹，一般映射的文件夹是用来为这个容器配置的文件夹。

![20200131230212.png](http://qny.smartcoder.club/bed/20200131230212.png)

这就是Docker的魅力？？？只需要在意需要的配置文件。

##### 3. 将米家的设备配置上去

详细见另一份文档




### THANKS
- [群晖HomeKit 篇十：群晖Docker快速搭建HomeBridge和HomeAssistant平台：让ios用户可以通过siri控制小米设备](https://post.smzdm.com/p/761097/)
- [家庭智能 HomeAssistant 四大组件的安装
](https://blog.csdn.net/catoop/article/details/89000020)
- [Docker镜像地址汇总地址](https://hub.docker.com/search?q=homeassistant&type=image)


