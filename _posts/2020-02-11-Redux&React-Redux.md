---
layout:     post
title:      Redux&React-Redux
subtitle:   
date:       2020-02-11
author:     5只猫
header-img: img/article-bg.jpg
catalog: true
tags:
    - 前端
---


# 1. Redux

- Redux是用于管理数据状态的js库。
- 可用于管理React中多个组件共享的数据的状态。

`对数据进行集中式管理`，例如把数据统一放到父级组件里，但随着项目越来越大，单独的一个地方管理数据更好，就有了Redux。

# 1.1 Redux 原则

- 唯一数据源(通过一个store)
- 保持状态只读
- 数据改变只能通过纯函数实现 (reducers)

# 1.2 Redux 结构

![20200211103331.png](http://qny.smartcoder.club/bed/20200211103331.png)

![20200211133104.png](http://qny.smartcoder.club/bed/20200211133104.png)

`redux就是对一个仓库（store）的操作，我们可以布置好多仓位（state）（如： 水果、零食、糕点）等，他们每一个仓位（state）存储着不同的物品，每一个仓位都有一个操作系统（reducer）， 这个操作系统可以进行入库物品（ADD）、统计件数（COUNT）、筛选好（FILTER_GOOD）/坏（FILTER_BAD）等操作，这些操作都用一个统一的标签（type）来表示。`

# 1.3 Redux 使用

## 1.3.1 安装Redux模块
~~~
npm install --save redux
~~~

## 1.3.2 创建一个store对象 createStore()

`创建一个store仓库，同时刷好这个仓库的操作系统。(绑定reducers方法)`

~~~
import {createStore} from 'redux'
import {deal} from './redux/reducers.js'

let store=createStore(deal);
~~~

# 1.3.3 向store获取数据 store.getState()

`向store仓库，取货物(取数)`

~~~
let text=this.props.store.getState();
~~~


# 1.3.4 向store请求操作 store.dispatch(action)

`向store仓库，添加一个货物(action.type=="add")`

`从store仓库，拿掉一个货物(action.type=="delete")`

~~~
store.dispatch({
    type:'add',
    data:1
});
~~~

# 1.3.5 订阅更新数据 store.subscribe(listener)

`当store仓库货物发生变化时，从新盘点数据(渲染数据)`

~~~
store.subscribe(
    function(){
        render();  
    }
)
~~~

# 1.4 优化代码

将Redux中具体的实现给模块化。

![20200211220103.png](http://qny.smartcoder.club/bed/20200211220103.png)



# 2. React-Redux

由于Redux与Reduct组件之间耦合性太强，在组件中有很多操作store的方法，代码不够简洁。
![20200211220414.png](http://qny.smartcoder.club/bed/20200211220414.png)

## 2.1 react-redux是什么？

- 一个react插件库
- 专门用来简化react应用中使用redux

## 2.1 安装
~~~
npm install --save react-redux
~~~

# THANKS

- [React-Redux](https://www.jianshu.com/p/ad7eddb23d66)