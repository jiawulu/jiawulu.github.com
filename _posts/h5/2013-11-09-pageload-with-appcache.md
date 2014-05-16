---
layout: post
title: "pageload with appcache"
description: "研究分析在appcache的情况下页面资源的加载情况"
category: "webapp"
tags: [webapp,appcache,network]
---
{% include JB/setup %}

appcache 在页面2次访问的时候一边会从本地（一般存在sqllite下）读取资源，快速的把页面呈现出来;另一边会检查manifest本身是否发生变更,如果变更的话则默默的进行资源的更新操作，这些更新都是基于http本身的cache，如果顺利的话会形成新的版本，并发出通知appcache已经更新到最新版本了。

但是当我们首次访问一个带有appcache的页面的时候，又是一个怎样的情况，我还没找到太多明确的答案。不知道你们是否也和我一样心存疑虑呢？

## 框架页面

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=938jJHG3g&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

这个页面非常简单，主要有几个主要的测试要素：

1. 申明使用manifest
2. 外链了一个js，再内敛一段css，再外链一段js，再内敛一段js
> 观察内外链js的资源和appcache中的资源加载情况
3. 最后注册了一个page load的，当资源加载完成后触发

## manifest

    CACHE MANIFEST
    ##1383824066156
    CACHE:
    http://a.tbcdn.cn/mw/base/styles/component/taoplus/css/taoplus2.css?v=290727338_5266
    http://a.tbcdn.cn/mw/webapp/my/js/index.js?v=69911886_108957
    http://a.tbcdn.cn/mw/base/utils/server/mh5init.js?v=895381892_2866

    http://g.tbcdn.cn??/mtb/zepto/1.0.2/zepto.js,/mtb/mixsln/0.4.3/mixsln.js,/mtb/lib-h5init/1.0.0/h5init.js,/mtb/lib-mtop/0.5.7/mtop.js,/mtb/lib-login/0.2.3/login.js,/mtb/lib-mtop/0.5.7/logined.js,/mtb/lib-mtop/0.5.7/plugin/bigpipe.js,/tb/fdc/aplus_h5.js?v=2207103287_100404

    http://a.tbcdn.cn/mw/base/libs/seajs/1.2.0/sea.js
    http://a.tbcdn.cn/mw/base/libs/underscore/1.3.3/underscore.js
    http://a.tbcdn.cn/mw/base/libs/zepto/1.0.0/zepto.js
    http://a.tbcdn.cn/mw/base/libs/backbone/0.9.2/backbone.js
    http://a.tbcdn.cn/mw/base/libs/mustache/0.5.0/mustache.js

    http://a.tbcdn.cn/s/aplus_wap.js

    http://a.tbcdn.cn/app/sns/img/default/avatar-40.png

    http://a.tbcdn.cn/mw/base/styles/component/more/images/loading.gif

    http://a.tbcdn.cn/apps/mytaobao/3.0/profile/defaultAvatar/avatar-160.png
    http://a.tbcdn.cn/mw/app/mytaobao/h5/images/star.png
    http://a.tbcdn.cn/mw/webapp/fav/img/grey.gif
    http://a.tbcdn.cn/mw/base/styles/component/icon/images/w_3.gif
    http://a.tbcdn.cn/mw/base/styles/component/icon/images/w_5.gif
    http://a.tbcdn.cn/mw/s/mi/icons/trade/mall.png
    http://assets.taobaocdn.com/sys/common/icon/trade/xcard.png
    http://img03.taobaocdn.com/tps/i3/T1lTxTXptdXXb1upjX.jpg
    NETWORK:
    *
    FALLBACK:

manifest里边故意申明了很多需要appcache的资源，我们也特地的留意一下它包含的内容

1. css，png，gif，js 资源
2. 申明是有顺序的

## 加载分析

下边是用galaxy s3（4.1.2），chrome 浏览器的第一次访问的资源加载情况，如下：

![resource load analyze](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=538jJHF76&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

我的解读如下：

1. 加载框架页面,这应该是一个流式的加载过程，会从上到下的依次加载资源，如果途中遇到外链的，则会发出外链的请求，是一个并行的过程。
2. 因为manifest 和 第一个js申明的位置比较靠近，都在最上边，所以它们是最早发出外链请求的。
3. 因为manifest文本内容较少，它很快就会返回了，这样整个appcache就有了目标，该去缓存哪些资源。因此会很快的主动去加载那些需要被cache的资源，如图中的 4,5,6,7,8... 
4. 接下来图中的大多数资源都是appcache主动发起的请求，从顺序上看明显和 manifest 文件中申明的顺序不一致，好像是比较随机的，但是`aplus_wap.js` 并没有在之后出现。 我估计是浏览器在获取到manifest文件之后，会根据当前时间点的加载情况，去判断还有哪些资源还没有开始下载，如果没有则发出请求。 如果已经在下载队列中，则会复用那条下载请求
5. 注意一下红色的`underscore.js`，这儿重复出现的原因是html的内敛内容比较多，当浏览器下载html到这个外链的时候，其实appcache已经把它下载好了，但是因为整个流程还没完成，所以appcache这个时候还是不可用的，但是http cache已经好了。 当html需要这个资源的时候，就会再次请求，被http cache 命中，返回。
6. 当html中的资源全部加载好之后，立刻触发onload事件，整个页面加载过程结束。但是appcache的后台并行线程还在继续
7. 当所有的appcache中的文件清单全部下载好，形成版本的话，则完成了一次顺利的离线话，如果异常则会重新再跑一遍流程，只是部分的资源可能会直接从http cache中获取到了


## 总结

1. appcache是完全一个背后的线程，不会影响任何正常的页面的加载过程
2. appcache在会对主页面的加载产生一定的影响的，主要在带宽上，就这次实验而言，影响还是比较大的，但是也是可以优化的
3. appcache会尽量复用已经加载的资源
4. appcache可能会造成资源的重复下载，但是代价不大
