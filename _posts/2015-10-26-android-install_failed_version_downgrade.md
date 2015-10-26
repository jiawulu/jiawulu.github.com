---
layout: post
title: "android INSTALL_FAILED_VERSION_DOWNGRADE"
description: "INSTALL_FAILED_VERSION_DOWNGRADE 问题排查"
category: 
tags: [android,issue]
---

今天更新的时候遇到INSTALL_FAILED_VERSION_DOWNGRADE（命令行 adb install -r）,想到可能是versionCode 不一致，找了下aapt这个工具可以获取到apk的version，记录下

## aapt

$ANDROID_SDK/android-sdk-macosx/build-tools/23.0.0/aapt

### 获取手机安装的apk

adb pull /data/app/com.xx.xx-1/base.apk .


### 解析

aapt dump badging base.apk

    package: name=’com.xx.xx’ versionCode=’123’ versionName=’5.4.3.1’ platformBuildVersionName=’’
    install-location:’auto’
    sdkVersion:’14’
    targetSdkVersion:’18’


{% include JB/setup %}
