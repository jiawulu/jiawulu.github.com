---
layout: post
title: "http cache study"
description: "http cache研究实践"
category: "webapp" 
tags: [webapp,http,cache]
---
{% include JB/setup %}

关于http cache，网上有很多的资料来介绍，主要还是 Cache-Control,Expores,Last-Modify,Etag等header的灵活运用。

推荐： [ 浏览器缓存详解:expires,cache-control,last-modified,etag详细说明](http://blog.csdn.net/eroswang/article/details/8302191)

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=U38jLHRPz&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

这篇文章非常深入浅出，结论如下：

1. 当2次访问页面时候，会优先走本地cache，前提就是有没有失效
2. 当刷新的时候，浏览器会跳过本地cache，发出http请求，带上`If-Modified-Since`的header 头,服务器端会根据这个进行304的优化
3. 当强制刷新的时候，会强制发出http请求，并且没有`If-Modified-Since`头，因此是一个全新的请求。

带着这些基础知识，我试验了下我们的H5detail页面，发现有点出入，事实并非如此。但我普通刷新单个页面的时候，发现有的资源走了正常的304，这个ok；有的资源却是直接from cache了，这个引起了我们的注意。

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=D38jLHBgE&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

既然有这些疑问，我准备一探究竟。

## html页面

    <!DOCTYPE html>
    <html>
    <head>
        <title>http cache test</title>
        <script src="http://g.tbcdn.cn/mtb/zepto/1.0.2/zepto.js"></script>
        <script src="http://a.tbcdn.cn/s/aplus_wap.js"></script>
        <script src="http://a.tbcdn.cn/cps/trace.js"></script>
        <script src="http://lu.m.taobao.com/expires.js?v=123"></script>
    </head>
    <body>
    hello world
    </body>
    <script>
        var s = document.createElement("script");
        s.type = "text/javascript";
        s.src = "http://lu.m.taobao.com/expires.js?v=2";
        $("head").append(s);
    </script>
    <script>
        window.addEventListener("load", function () {
            var s = document.createElement("script");
            s.type = "text/javascript";
            s.src = "http://lu.m.taobao.com/expires.js";
            $("head").append(s);
        })
    </script>
    </html>

这是一个非常简单的页面，通过3种方式来引用资源：

1. 正常的script 便签外链
2. 在页面的html解析执行过程中动态添加
3. 在页面加载完成之后（after page load）再动态添加脚本

## 测试结果分析

1. 我们通过正常进入的方式2次访问页面的时候，一切都很正常，因为这些资源都还未过期，因此结果都是from cache

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=638jLHCKb&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

2. 如果我们刷新进入的话，情况就出现了差异化

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=H38jLHRZI&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

由于expires.js 服务端都没有实现304的功能，所以尽管带上了 `If-Modified-Since` 头，服务端返回的都是200， 我们注意到expire.js (在页面load之后加载的，可以)是from cache的，其余的所有资源都进入了第2个阶段，带着 If-Modified-Since 请求服务器。

3. 强制刷新的，又是这般景象

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=K38jLHBgN&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

一般的资源都很正常的遵循规律，只有在page load之后加载的资源会从最低层http cache走起。

## 总结

http cache在通常情况下是按照规律来的，会根据不同的情况选择不同的策略。 当资源的加载时间位于page load之后，那么这些策略将不起作用；http cache将始终发挥它的作用！

## 启发

根据业务状况，我们可以通过在pageload之后去动态加载资源的方式以实现http cache的最大作用

## code

代码在github，有兴趣的可以研究下，欢迎交流指正。 [https://github.com/goodtools/httpserver](https://github.com/goodtools/httpserver)
