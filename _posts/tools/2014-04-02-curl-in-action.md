---
layout: post
title: "curl in action"
description: "curl basic use"
category: "tools"
tags: [curl]
---
{% include JB/setup %}

## curl 的基础使用

### echo

    curl http://hws.m.taobao.com/cache/wdetail/5.0/\?id\=37350344364

### 查看response Header

    curl -I  http://hws.m.taobao.com/cache/wdetail/5.0/\?id\=37350344364

### 自定义header，指定host

    curl -H "Host:hws.m.taobao.com"  http://42.156.219.4/cache/wdetail/5.0/\?id\=37350344364
