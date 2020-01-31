---
layout:     post
title:      设备接入HomeAssistant
subtitle:   
date:       2019-11-11
author:     5只猫
header-img: img/article-bg.jpg
catalog: true
tags:
    - HomeAssistant
---

# 家里设备清单
- 枢纽类
    - [ ] 小爱同学mini
    - [ ] 小爱智能闹钟
    - [ ] 斐讯智能语音助手
- 插座类
    - [ ] 斐讯智能插座DC1
    - [x] 米家智能插座ZigBee版本
    - [x] 青米智能插座
- 环境类
    - [x] 米家空气净化器2s
    - [ ] 斐讯M1空气监测仪
    - [x] 米家蓝牙温湿度传感器
- 生活类
    - [x] 米家电饭煲
    - [x] 米家直流电风扇1X
    - [ ] 米家摄像头
    - [x] 米家空调伴侣一代(网关不能用)
    - [x] 米家台灯
- 网关类
    - [x] 米家无线网关升级版本
    - [x] 米家无线开关
    - [x] 门窗传感器
    - [x] 人体传感器


# 获取米家设备Token 
目前新版本的米家APP已经不支持获取miio2.db文件。
可以使用模拟器或者root的设备上安装旧版本的米家APP就行。

#### 1. 登录旧版本米家

APP Version: 5.0.10

我只知道这个版本可以。

![20200131230340.png](http://qny.smartcoder.club/bed/20200131230340.png)

#### 2. 找到miio2文件

所谓miio2.db文件也就是本地的数据库文件，毋庸置疑token这些敏感的信息就在里面

PATH: /data/data/com.xiaomi.smarthome/databases/miio2.db

#### 3. 安装DB.Browser.for.SQLite-3.10.1-win64

将文件拖到软件里

![20200131230418.png](http://qny.smartcoder.club/bed/20200131230418.png)

Token在此。

![20200131230454.png](http://qny.smartcoder.club/bed/20200131230454.png)

#### 4. Token列表


设备名字 | IP | MAC | Token 
---|---|---|---


# 将设备配置到HomeAssistant上

- #### 空气净化器
HomeAssistant 已集成，只需在configuration.yaml里配置就行。

[空气净化器官方配置](https://www.home-assistant.io/components/fan.xiaomi_miio/)

```
# 空气净化器
fan:
# Example configuration.yaml entry
  - platform: xiaomi_miio
    host: 192.168.1.201
    token: 2xxxxxxxxxxxxxxxxxxxxxxxxx3
```


- #### 米家空调伴侣

由于米家空调伴侣没有集成，所以需要自己下载对应的组件包，放到配置文件夹下custom_components文件夹就行。

![20200131230530.png](http://qny.smartcoder.club/bed/20200131230530.png)

[组件下载地址
](https://github.com/syssi/xiaomi_airconditioningcompanion)

同时也需要在configuration.yaml里配置

```
# 空调伴侣
climate:
  - platform: xiaomi_miio_airconditioningcompanion
    name: Aqara Air Conditioning Companion
    host: 192.168.1.168
    token: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    target_sensor: sensor.temperature_158d0001f53706
    scan_interval: 60
```


- #### 青米插线板

已集成，只需配置。

```
# 青米插线板
switch:
  - platform: xiaomi_miio
    name: chuangmi.plug.m1 
    host: 192.168.1.16
    token: xxxxxxxxxxxxxxxxxxxxxxxxx
```


- #### 米家台灯
虽然已经集成，但是米家台灯需要到软件中打开局域网控制才可以。

![20200131230636.png](http://qny.smartcoder.club/bed/20200131230636.png)

![20200131230659.png](http://qny.smartcoder.club/bed/20200131230659.png)

```
discovery:
  ignore:
    - yeelight
yeelight:
  devices:
    192.168.1.177:
      name: 台灯
```

[新版台灯写法](https://www.home-assistant.io/components/yeelight/)

- #### 米家无线网关升级版
已集成，在米家APP中得到KEY值，配置即可。

米家网关会把已经配好的子设备默认带到homeassistant上，所以子设备无需配置。

```
xiaomi_aqara:
  discovery_retry: 5
  gateways:
    - key: xxxxxx 
```

[米家无线网关升级版](https://www.home-assistant.io/components/xiaomi_aqara/)


- #### 米家电饭煲

[插件地址](https://github.com/syssi/xiaomi_cooker)

官方未集成，不过可以下载插件，只需要将插件里的xiaomi_cooker文件夹拖到custom_components文件夹下就行。

![20200131230801.png](http://qny.smartcoder.club/bed/20200131230801.png)

配置：
```
# 小米电饭煲
xiaomi_cooker:
  name: Xiaomi Rice Cooker
  host: 192.168.1.236
  token: xxxxxxxxxxxxxxxxxxxxxxxx
  model: chunmi.cooker.normal2  

```

显示效果：目前只能监测，还不能控制。

![20200131230848.png](http://qny.smartcoder.club/bed/20200131230848.png)


- #### 米家直流电风扇

因为买的是新款的，所有插件作者死活没有更新，不过好有人fork了作者的版本，重新改了一下。


[bieniu/xiaomi_fan](https://github.com/bieniu/xiaomi_fan)

tips:

1. 需要更新python-miio库，可以理解为这些插件是在一个基本库上实现的，库需要更新来支持一些新的设备。
2. 因为我用的是docker，所以需要进入docker 容器执行脚本。



# 开启自带HomeKit

由于新版本已经支持HomeKit功能，所以实际上HomeBridge可以不用装了，在yaml文件中加上一行代码，就会自动生成一个homekit码了。


```
# 开启homekit
homekit:
```

重启后，在hass面板上有homekit的配对信息。

![20200131230933.png](http://qny.smartcoder.club/bed/20200131230933.png)


这里有一个坑，Homekit需要组播，所以在创建容器的时候，网络得用Host。

![20200131231113.png](http://qny.smartcoder.club/bed/20200131231113.png)

[[HomeKit] 在docker下homekit无法发现“配件”](https://bbs.hassbian.com/thread-6559-1-1.html)


使用组播网络方式的homeassistant写法：
```
docker run -d --name homeassistant --restart=always --net=host -v /var/lib/docker/2homeassistant/config:/config homeassistant/aarch64-homeassistant:latest
```


# THANKS

 - [HomeAssistant支持的设备](https://www.home-assistant.io/components/#system-monitor)