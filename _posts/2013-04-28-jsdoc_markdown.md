---
layout: post
title: "使用jsdoc 和 markdown 来生成js 文档"
description: ""
category: "wiki"
tags: [javascript,jsdoc,markdown]
---
{% include JB/setup %}

一直以来我都不喜欢写注释，主要原因是：

1. 文档难以维护，容易造成注释和代码不匹配
2. 坚持良好清晰的代码是最好的文档

现在为什么还要重新讨论注释呢？ 主要原因是如果在团队内部，我们可以很好的了解代码，那么是没有问题的。

但是如果我们想把我们的代码沉淀下来，推出去让更多的其他同学认识和了解，那么你直接丢给他们一份代码确实不是那么合适。所以这几天就研究了下这方面

##我理想的文档生成器

1. 代码和文档是不可分割的，最好在编写源码的过程也完成了文档的编写
2. 文档的形式是灵活的，必须包含下面的内容：
> 可以描述的创造思路
>
> 清晰的api 文档
>
> 代码样例
3. 学习成本不高


##一些解决方案

在网上找了下资料，发现这篇还是不错的。

[Javascript Documentation Tools – JSDoc, YUIDoc, Dox, JSDuck](http://spencer.kokiya.com/javascript-documetation-tools-summary/)

它主要分析了 4 种不同的文档生成工具，作者也没有明确的指出哪种更好，具体的还是要靠使用者自己体验


##jsdoc

首先推荐几个网址方便大家快速的对jsdoc 有个快速的了解

1. [使用jsdoc生成组件API文档—jsdoc实战](http://www.36ria.com/5101)
2. [官方文档](http://usejsdoc.org/)
3. [开源代码](https://github.com/jsdoc3/jsdoc3.github.com)

##实践

### 模块

    /**
     * @author 武仲(wuzhong@taobao.com)
     * @since 2013.4.28
     * @module  history/history
     * @description
     *
     * #背景 
     *   markdown here
     */
    define(function (require, exports, module) {

具体的注解我就不解释了，大致如下：

1. 首先一个js 文件对应一个模块，这个我觉得是没有意义的，类似于 java 中的package，一个模块下可以对应多个的class
2. description 这个可以理解为对模块的描述，这里有很大的自由发挥空间，可以为任意的文本，对我来说最重要的是这里支持 **markdown** 语法，这样就能很方便的写出丰富的描述信息了


### class

    /**
     * @name History
     * @requires $
     * @class 通用的全站返回解决方案
     * @example
     *  new History({
     *   defaultPage:"http://m.taobao.com",
     *   defaultHash:"#index",
     *   defaultSeparator:'/',
     *   fromBack:function () {
     *       return h5_comm.isLogin() && 0 == document.getRefer().indexOf("http://login");
     *   },
     *   setItem:function (key, value) {
     *       tbh5.set(key, value);
     *   },
     *   getItem:function (key) {
     *       return tbh5.get(key);
     *   },
     *   validate:function () {
     *       return location.hash.indexOf("&") == -1;
     *   }
     * });
     * @constructor
     * @param {Object} options 配置参数
     * @param {String} options.defaultPage  默认返回的页面
     * @param {String} options.defaultSeparator  默认的分隔符，默认是 -
     * @param {String} options.defaultHash 设置首页的hash，默认是 #index
     * @param {Function} options.filterEntrance  过滤页面是否可以返回
     * @param {Function} options.setItem  覆盖持久化history的方法
     * @param {Function} options.getItem  覆盖从持久层恢复的方法
     * @param {Function} options.fromBack 是否是从通过返回回到这个app的
     * @param {Function} options.validate 验证hash，是否要加入到堆栈内
     */
    function History(options) {

1. class 表示这是一个类
2. example 这里我们可以贴入一些使用的例子，方便快速的上手
3. constructor 表示构造器
4. 参数 @param {type} 参数名称  对应含义， jsdoc 会帮助你自动生存api 表格
5. require 可以指出间接依赖的模块


### 方法

    $.extend(History.prototype,
    /** @lends History.prototype */{
        _originalHistory: window.history,
        defaultHash: "#index",
        defaultSeparator: "",
        /**
         * 通用的返回方法，具有层级关系
         */
        back: function () {


1. `@lends` 表示下边 block  中对应的方法或者属性属于 History
2. 方法上的注释只要普通的注释就可以，这样就能明确好 class 和 function 的结构了


## jsdoc 的配置和运行

    {
        "source": {
            "include": ["modules/history"],
            "exclude": [],
            "includePattern": ".+\\.js(doc)?$",
            "excludePattern": "(^|\\/|\\\\)_"
        },
        "plugins": [ "plugins/markdown" ],

        "markdown": {
            "parser": "gfm",
            "hardwrap": true
        }
    }

这里我们使用markdown 插件，不然jsdoc 本身是不支持的
由于目前的工程代码比较多，有文档的地方也不多，这样我们可以明确制定哪些模块需要进行文档生成，具体看 source.include 节点

最后是一个简单的bash

    #!/bin/bash

    jsdoc -c conf.json



