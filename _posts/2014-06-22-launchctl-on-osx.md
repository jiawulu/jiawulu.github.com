---
layout: post
title: "launchctl on osx"
description: "利用launchctl实时的更新动态域名"
category: "tools"
tags: [shell,osx]
---
{% include JB/setup %}

### 更新ip的脚本程序

	#!/bin/bash
	
	localip=`show_ip.sh`
	remoteip=`host jiawulu.3322.org | awk '{print $4}'`
	
	if [ -n $localip ] && [[ $localip !=  $remoteip ]]; then
	    ## do your task here
	fi


### 任务脚本 

#### 目录

	~/Library/LaunchAgents

#### plist

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
	  <key>Label</key>
	  <string>org.3322.jiawulu</string>
	  <key>ProgramArguments</key>
	  <array>
	    <string>/Users/wuzhong/softs/bin/sync_jiawulu.3322.org.sh</string>
	  </array>
	  <key>StartInterval</key>
	  <integer>600</integer> <!-- 单位是秒，这里也就是10minutes -->
	</dict>
	</plist>


### 启动任务

	➜  LaunchAgents  launchctl load org.3322.jiawulu.plist
	➜  LaunchAgents  launchctl start org.3322.jiawulu
	
	
### 关闭任务

	➜  LaunchAgents  launchctl stop org.3322.jiawulu
	➜  LaunchAgents  launchctl remove org.3322.jiawulu