---
layout: post
title: "webapp plugin standard"
description: "webapp插件化开发规范"
category: "webapp"
tags: [webapp,plugin,mix]
---
{% include JB/setup %}
webapp插件化开发和其他的web开发一样，开发者还是需要用html，js，css，template等要素构建基本的网页。只不过不同于以往的完全自由，插件需有被容器加载，管理其生命周期，因此插件必须符合一定的规范。我们暂且就叫它为插件规范.

我们首先概览一下插件开发的整体流程，如图：
![开发流程](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=I35jIHu8H&pInfo=A3YjLHQv&showBig=true&app_name=)
开发者需要注意的是下面几个部分：

1. 初始化运行环境
2. 增加插件定义
3. 具体业务实现
4. 通过插件后台进行插件发布

## 初始化环境
### 容器
插件是运行在容器当中的，所以第一步就是在本地初始化一个容器。容器一般都是固定的，插件开发者不能修改它的核心源码，也不能在容器上放和插件业务相关的任何代码。

容器的核心如下：

1. 整体的html结构，包括 navi bar, content , tool bar. 这个是固定的，插件开发者不能直接的操作这些元素，容器框架会提供 `page.$el` 给插件开发者使用，这样开发者就可以安全的操作那块属于它的区域了。
2. zepto， 基础工具，少不了
3. mtop sdk。 目前而言各个插件都有依赖，暂时提升到容器层
4. mix 框架。 mix 框架是容器的核心实现，它的主要作用如下：
>1. 统一导航
>2. 统一页面切换
>3. 负责插件(page)的什么周期
>4. scroll,gesture等

mix的更深入了解请访问： [https://github.com/mixteam/mixsln](https://github.com/mixteam/mixsln)

### demo

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=G35jIHx2p&pInfo=A3YjLHQv&showBig=true&app_name=)

### 工具
为了简化整个webapp的开发，这个过程中我们可以借助于工具来提高我们的效率：

1. nodejs
2. npm install grunt-cli, 关于grunt的使用可以参考我的一篇[介绍文章](http://jiawulu.github.io/web/2013/05/26/grunt_introduce/)
3. mix-tool, 专门用来初始化容器，创建插件

#### mix-tool

1. npm install -g mix-tool

    $ mix-tool  -h

      Usage: main.js init|createpage|httpserver|demo [options] [value ...]

      Options:

        -h, --help            output usage information
        -V, --version         output the version number
        -n, --name <string>   plugin's name
        -t, --title <string>  plugin's title at the top navi bar
        -r, --route <string>  plugin's route, can be a regex
        -p, --port <n>        http server port,default is 80

2. 初始化容器：  `$ mix-tool init`， 这样就在当前目录上生成了　index.html
3. 增加一个插件： `$ mix-tool createpage -n test -t "TEST"`

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=H35jIHy4o&pInfo=A3YjLHQv&showBig=true&app_name=)

4. 启动http server 或者放到今天服务器上，访问：http://localhost/index.html#abc
5. 要想了解更详细的demo ，推荐访问mixln的github主页或者 ： `mix-tool demo`

## 增加插件


## 具体业务
### 依赖

### templates

## 发布到cdn

## 插件发布

