---
layout: post
title: "linux_shell"
description: "basic linux shell usage"
category: "shell"
tags: [linux,shell,tools]
---
{% include JB/setup %}

> 整理了之前写过的一些shell脚本，重新温习了下,做个备忘

## awk
> `awk` 是用来做字符串分割的，默认分隔符是空格，非常类似于java string 的splite 方法

    luu:bin/ (master) $ more showip.sh
    #!/bin/bash

    ifconfig| grep "255.255.255." | awk '{print $2 }'

在这个例子里我的理解是获取空格分割的第3个字符串

## ed
> `ed` 是用来做字符串替换的，大致的工作流程是：

### step
1. 打开文件
2. 进行文本的替换
3. 保存文件

### demo
    ed -s $JBOSS_CONF << EOF
    g/searchfile/s/search/replace/g
    w
    q
    EOF

## expect

## file

## args
