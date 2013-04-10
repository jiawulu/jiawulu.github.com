---
layout: post
title: "chrome_profiles"
description: "how to profile html5 app"
category: "javascript"
tags: ["javascript","chrome","profile"]
---
{% include JB/setup %}

    最近主要做了webapp的适配以及客户端的融合，虽然功能大体上都完成了，但是对于性能这块一直都还没有做，并且内存溢出有可能导致客户端的崩溃，所以做些webapp内存方面的检查还是有些必要的。

    [chrome develop tool](https://developers.google.com/chrome-developer-tools/) 确实还是比较强大的，但是用了之后总体感觉也不是很好，还是有太多的疑惑，但是也能帮助解决一些问题

## console.log引发的leak

    
    s对象在comparison view中我们明显看到了它一直在显著的增长过程中，这应该是一个明显的溢出表现。
    展开S对象，我们看到这是一个iscroll对象，也就是说明iscroll对象没有被释放掉。

    那么如果去查找为什么不能被回收的原因呢？ 点击 @1082823 元素，检查它的依赖树，发现被一个global 所依赖着，但是具体这个global是什么东西还真是看不出来。

    这个也就是工具不能给我解决的问题， **无法明确的指出溢出的原因**
,实在没办法只能看代码，最终定位到的问题如下：

```javascript```
    this.destroy = function () {
        console.log(myScroll);
        myScroll.destroy();
    }

    是的，iscoll对象被 console 所引用了～，很肯爹。虽然一般线上环境都会去掉console ， 但是通过这个过程也体验了 profile 能够带给我们的发现溢出的点。 至于具体的找到溢出的原因还要看各自的分析了。

    推荐下这篇文章，还是不错的：[JS内存泄漏排查方法——Chrome Profiles](http://h5dev.uc.cn/article-25-1.html)

## 桌面浏览器 VS 手机浏览器



## compiled code & array
