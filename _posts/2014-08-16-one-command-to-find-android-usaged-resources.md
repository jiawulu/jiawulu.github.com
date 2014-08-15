---
layout: post
title: "one command to find android usaged resources"
description: ""
category: "android"
tags: [shell,android]
---
{% include JB/setup %}

### 查找使用的layout

    find src* -iname "*.java" | xargs more | egrep -o "R.layout.[a-z1-9_.]*" | awk -F "\." '{print "res/layout/"$3".xml"}'

### copy出来

    name "*.java" | xargs more | egrep -o "R.layout.[a-z1-9_.]*" | awk -F "\." '{print "res/layout/"$3".xml"}'| uniq | xargs -I {} cp -rf {} layout

