---
layout: post
title: "android_env_prepare(1)"
description: "linux 下android开发环境准备"
category: "android"
tags: [android,startup]
---
{% include JB/setup %}

## android-sdk 

1. 下载各个版本
2. 配置adb 到path下

### 添加手机

1. lsusb获取到手机的相关数据

    Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
    Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 001 Device 004: ID 0000:0000  
    Bus 001 Device 003: ID 04d9:a096 Holtek Semiconductor, Inc. 
    Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
    Bus 003 Device 008: ID 05c6:9031 Qualcomm, Inc. 
    Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

2. 添加权限相关的配置

    ~/github/jiawulu.github.com/_posts(master ✗) more /etc/udev/rules.d/70-android.rules 
    SUBSYSTEM=="usb", ATTR{idVendor}=="2717",ATTRS{idProduct}=="9309", MODE="0666"
    SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0666", GROUP="plugdev" 

3. adb devices

    ~/github/jiawulu.github.com/_posts(master ✗) adb devices
    List of devices attached 
    69312eb device

## 模拟器

1. avd
2. genymotion

> 速度比较快，值得推荐

## android-studio

![](http://yunpan.alibaba-inc.com/share/json/GetPhotoTag.do?info=B39j5HCr8&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

## hello world

1. maven 3
2. mvn archetype:generate -DarchetypeArtifactId=android-quickstart


这样导入的工程比较简洁，接下来可以正式的开发学习了～

