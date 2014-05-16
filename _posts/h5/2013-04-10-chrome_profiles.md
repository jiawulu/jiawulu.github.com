---
layout: post
title: "chrome_profiles"
description: ""
category: "webapp"
tags: [javascript,webapp,chrome,profile]
---
{% include JB/setup %}

最近主要做了webapp的适配以及客户端的融合，虽然功能大体上都完成了，但是对于性能这块一直都还没有做，并且内存溢出有可能导致客户端的崩溃，所以做些webapp内存方面的检查还是有些必要的。

[**chrome develop tools**](https://developers.google.com/chrome-developer-tools/) 确实还是比较强大的，但是用了之后总体感觉也不是很好，还是有太多的疑惑，但是也能帮助解决一些问题


## console.log引发的leak
![mem leak](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=Q32jZHFlv&pInfo=A3YjLHQv&showBig=true&app_name=)

s对象在comparison view中我们明显看到了它一直在显著的增长过程中，这应该是一个明显的溢出表现。

展开S对象，我们看到这是一个iscroll对象，也就是说明iscroll对象没有被释放掉。

那么如果去查找为什么不能被回收的原因呢？ 点击 @1082823 元素，检查它的依赖树，发现被一个global 所依赖着，但是具体这个global是什么东西还真是看不出来。

这个也就是工具不能给我解决的问题， **无法明确的指出溢出的原因**

实在没办法只能看代码，最终定位到的问题如下：

    this.destroy = function () {
        console.log(myScroll);
        myScroll.destroy();
    }

是的，iscoll对象被 console 所引用了～，很肯爹。虽然一般线上环境都会去掉console ， 但是通过这个过程也体验了 profile 能够带给我们的发现溢出的点。 至于具体的找到溢出的原因还要看各自的分析了。

推荐下这篇文章，还是不错的：[JS内存泄漏排查方法——Chrome Profiles](http://h5dev.uc.cn/article-25-1.html)

## 桌面浏览器 VS 手机浏览器
![desktop chrome closure](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=J32jZHdrm&pInfo=A3YjLHQv&showBig=true&app_name=)

刚开始在desktop环境下进行调试，走了不少弯路。上图中本来我就想观察下闭包是否会有增长的现象，结果直接吓了我一跳，原因是chrome 会加入很多自带的闭包。

所以还是直接用真机测试比较靠谱

    #!/bin/bash

    adb forward tcp:9222 localabstract:chrome_devtools_remote

### 一个比较有意思的现象
![jsonp leak](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=232jZHfvX&pInfo=A3YjLHQv&showBig=true&app_name=)

在该图中我们看到closure 有一些增长，展开来之后发现是jsonp的一些回调方法，这个也比较容易理解，在zeptojs中会设置一个超时阀值，过段时候这些闭包就会被自动销毁掉。

但是这个过程也是一个发现问题的过程，如果closure有明显增长，那么我们还是需要留意下的。


## compiled code & array
compiled code 和 array 有点过于高深了，完全看不明白里面的东西，但是从现象上来说是一直增长中的。

我在想是否是因为js 是解释性的，每次执行都有可能被重新编译？ 然而确没有及时释放？

还是有些疑惑。

## 总结
如果带着目标去发现问题我觉的chrome 工具还是可以的，比如我们明确一个类，去观察它是否存在内存泄露，但是如果想用这个工具来判断整个webapp是否是没有问题的？我觉的还是很难说的。



