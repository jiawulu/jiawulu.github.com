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
> `expect` 可以通过预制一些期望值和动作来进行自动化

    #!/bin/bash
    #用途： ssh连接服务器
    echo "host is $HOST USER is $USER password is $PASSWD"
    expect -c "
            spawn ssh -l $USER $HOST
            expect {
                "*yes/no*" { send yes\r\n; exp_continue }
                "*assword:*" { send $PASSWD\r\n; interact }
                "~]$ " { interact }
                eof { exit }
            }
     "

## file

### 文件监控

    inotifywait -mrq -e create,move,delete,modify --format %w%f $WATCH_DIR | while read F
    do
        $CMD $WATCH_DIR $DEST_DIR $F
    done

`F` 就是改变的文件或者文件夹

### 文件copy

    if [ ! -d $parent_dir ] ; then
        mkdir -p $parent_dir
    fi

    if [ -d $fileName ] ; then
        continue
    fi

    if [[ ! -f $toName || $(diff $fileName $toName) ]]; then	
        \cp $fileName $toName;
        echo "copy file from $fileName to  $toName";
    fi

## xargs

    ps -ef|grep $KEYWORD |grep -v "grep" | grep -v killall |awk '{print $2}' | xargs kill -9

`xargs` 可以把参数循环传入执行

## getoptso

    ## myfile_watch.sh -f www/aa -t www/aa_bak"
    WATCH_DIR=""
    DEST_DIR=""
    CMD="mycp.sh"
    while getopts t:c:f:h opt
    do
        case $opt in
            f) WATCH_DIR=$OPTARG;;
            t) DEST_DIR=$OPTARG;;
            c) CMD=$OPTARG;;
            \?|h) echo "$USAGE"; exit 1;;
    esac
    done

## 语法
### for in
    for fileName in $(find $from | grep -v ".svn" ) ; do
        mycp.sh $from $to $fileName
    done

### replace
    toName=${fileName/$from/$to};

### execute
    parent_dir=`dirname $toName`

