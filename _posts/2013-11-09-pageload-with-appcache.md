---
layout: post
title: "pageload with appcache"
description: "研究分析在appcache的情况下页面资源的加载情况"
category: "webapp"
tags: [webapp,appcache,network]
---
{% include JB/setup %}

appcache 在页面2次访问的时候一边会从本地（一般存在sqllite下）读取资源，快速的把页面呈现出来;另一边会检查manifest本身是否发生变更,如果变更的话则默默的进行资源的更新操作，这些更新都是基于http本身的cache，如果顺利的话会形成新的版本，并发出通知appcache已经更新到最新版本了。

但是当我们首次访问一个带有appcache的页面的时候，又是一个怎样的情况，我还没找到太多明确的答案,因此做了个实验
