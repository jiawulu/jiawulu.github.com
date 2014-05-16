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
4. mix 框架。 mix 框架是容器的核心实现,mix 的主要功能如下：
    >1. 统一导航
    >2. 生命周期管理
    >3. 转场动画
    >4. gesture
    >5. scroll

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
    $ mix-tool -h  
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
增加一个插件非常简单，我们只需要使用mix的api `app.definePage(properties)` 就可以了。

### demo
    (function(app, undef) {
        app.definePage({
            name : 'test',
            title : 'TEST',
            route : 'abc',
            startup : function() {
                // implement super.startup
                this.html("hello world");
            },
            teardown : function() {
                // implement super.teardown
            }
        });
    })(window['app']);

这样我们就定义了一个最简单的插件了，如果要让该插件生效我们还需要把该插件 加入 到容器 `index.html` 中去，


    <!-- PAGE DEFINITION HERE , 线上环境该代码会被app自动生成 -->
    <!-- add:plugins --><script src="./test.js"></script>

    <script>
        app.start();
    </script>

这样基本就完成了hello world

### page的关键属性

在上面例子中，我们看到page有如下几个关键属性：

1. **name** ：page 对应的name，这个必须是全局唯一性的，因为容器获取page是通过  `page.get(name)` 来完成的
2. route ： 进入这个插件对应的hash，因为目前我们的webapp都是hash驱动的，虽然这个可以是字符串，但是最好也能保证能被唯一匹配，不会发生误匹配
3. title : 对应于导航栏的标题，这个可以随意取
4. rosources : 插件对应的一些资源依赖，在这儿声明，容器会在进入page的生命周期之前预先准备好资源

### page的生命周期

page 的生命周期具体参考 [page api](https://github.com/mixteam/mixsln/blob/master/doc/api.md#appmodulepage)

1. ready() 定义Page时被调用。
2. startup() 载入Page时被调用。
3. teardown() 卸载Page时被调用。
4. show(persisited) @param {boolean} persisited 显示Page时调用。当Page在缓存中时，persisited为true。
5. hide() 隐藏Page时调用。

下面是一个虚拟的生命过程：
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=J35jJHCam&pInfo=A3YjLHQv&showBig=true&app_name=)

## 具体业务
插件还是要为具体的业务服务的，如果我们把业务代码都写在插件定义中，那么插件本身的加载就会变得很重。所以我们的一个解决方案是：

1. 插件的定义尽量精简，只定义一些重要属性和生命周期方法
2. 具体的业务和关联的基础库全部通过动态加载做2次获取

### 依赖

通过以下方式在page中申明依赖并开启动态加载插件

    resources : {
        "js": [
            "http://a.tbcdn.cn/g/mtb/lib-container/namespace.js",
            "http://a.tbcdn.cn/g/mtb/lib-container/component.js",/** H5 COMPONENT */
            "http://a.tbcdn.cn/g/mtb/lib-mtop/0.0.2/mtop_all.js",
            "http://a.tbcdn.cn/g/mtb/lib-cart/0.0.1/cartDb.js",
            "../app-cart/dist/cart.js"
        ],
        "css": [
            "http://a.tbcdn.cn/g/mtb/lib-container/h5component.css",
            "../app-cart/dist/cart.css"
        ]
    },
    plugins: {
        dynamic: true
    },

在我们的整个依赖中，主要分成2大类：
1. 细粒度的基础组件
2. 业务相关的业务实现

也许我们会觉得资源太分散的话，请求会比较多。 但是请放心，我们会对资源加载做优化，目前已经完成的是 combo，这些请求都会被合并到一个请求中去，如：[http://a.tbcdn.cn/g/mw/??./plugins/common/cartbase_min.js,./plugins/cart/cart_min.js](http://a.tbcdn.cn/g/mw/??./plugins/common/cartbase_min.js,./plugins/cart/cart_min.js)

并且接下来可能会对基础组件的版本进行管控，并进行本地cache

### view

接下来我们引入view的概念，view在很多层面上和page差不多，具体的还请参考mix 的apidoc。

在page中我们定义了插件的生命周期，那在startup过程中具体的业务实现其实就是通过view来实现的，具体看代码片段：

1. 定义一个view
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=035jJHErZ&pInfo=A3YjLHQv&showBig=true&app_name=)

2. 在page 中使用view
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=S35jJHEmV&pInfo=A3YjLHQv&showBig=true&app_name=)

### 隔离污染

因为插件最终是要被append到页面html中的，无论js，css，template都是在一个window下，所以我们开发插件的时候也要时刻注意你的插件是否会影响到其他插件，主要思考大致有：

1. dom元素的id不能通用化，最好前面都带上插件的前缀
2. 插件总dom的选择使用 `$el.find()` , 而不是使用全局的 $ 选择
3. css的样式同样也只针对具体的插件
4. 页面的scroll使用app.scroll 函数，这里会统一处理是否有scroller 的情况

### templates

插件化开发和以往的webapp开发有一个明显的差异性在于我们不能修改html，因此这些模板也要通过其他的方式进行获取了。mix 原生支持template，但是有个问题是每个template都会独占一个http请求，太多的异步也会影响用户的体验。

为此我们的解决方案是把所有的模板都放到js里去，怎么生成js则依靠工具实现

#### arttemplate
artemplate是一款不错的模板工具，通过它可以把 html模板 转换成一个可以执行的js function，具体的可以在 [https://github.com/aui/artTemplate](https://github.com/aui/artTemplate) 了解。

如果使用grunt，还可以用 `grunt-atc` , 工具化的执行模板编译任务。

把该任务加到grunt 的 watch 中，修改的结构良好的模板立即就会被同步生成方法，效率很快。

#### mustache
mustache在grunt下也有 `grunt-mustache` 插件，该插件会把所有的模板转换成字符串，放置在一个json对象中

## 发布到cdn

插件测试完成，发布到cdn，主要的文件一般为下面3个：

1. page.js : 这个也就是插件的定义
>开发者要考虑保证它的足够精简  
>page中的依赖地址确保是线上的绝对或相对地址
2. 业务js，包括view
3. 业务css

发布完成之后我们就可以拿到插件对应的cdn地址了。

## 插件发布

### 注册插件
注册插件的过程非常简单，如下：
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=035jJHKd3&pInfo=A3YjLHQv&showBig=true&app_name=)
1. 插件名对应page中的name，会在服务端做唯一性检查
2. 分组对应于webapp中page的组，建立联系
3. 插件对应的cdn地址，在页面生成的时候会抓取下来内敛到页面去
4. 状态做下线处理

### 发布webapp容器
这一步插件开发者不需要做，而是webapp的独立维护者来完成。和普通的页面管理一样，开发者还是需要把页面的一些元数据定义之外，还需要额外的选择这个webapp对应的分组。
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=835jJHJRB&pInfo=A3YjLHQv&showBig=true&app_name=)
组： 一个虚拟的概念，是某几个相关业务的集合。 比如：
1. trade  交易组，包括购物车，下单，详情
2. we  微淘， 微淘的核心页面，包括账号，feed，评论，首页
3. we_plugin 微淘插件

这样之后，每次页面生成的时候，都会去检查它对应组下的所有插件定义，自动的把它内敛到页面中去。
![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=J35jJHKKG&pInfo=A3YjLHQv&showBig=true&app_name=)

### TODO

目前的插件管理还是比较简单，下面还有一些需要做的事情：

1. 智能解析page，对插件中的name，title，route，resoureces 都进行管理
2. 在上一步的基础上，建议依赖管理树。统一依赖管理
3. url跳转统一控制，不然插件上下线还是需要修改其他插件
4. 插件动态加载和执行的性能监控和分析
5. 统一的资源cache方案，加速插件的2次加载

