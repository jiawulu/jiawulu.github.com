---
layout: post
title: "dnsmasq smarthosts"
description: "install and config dnsmasq in mac osx"
category: tools
tags: [dns,dnsmasq]
---
{% include JB/setup %}

## install

    brew install dnsmasq

## run it

    cp /usr/local/opt/dnsmasq/dnsmasq.conf.example /usr/local/etc/dnsmasq.conf
    sudo cp -fv /usr/local/opt/dnsmasq/*.plist /Library/LaunchDaemons
    sudo launchctl start homebrew.mxcl.dnsmasq

## /etc/resolv.conf

    ➜  ~  more /etc/resolv.conf

    nameserver 127.0.0.1
    nameserver 223.5.5.5
    nameserver 223.6.6.6

dns 的快速切换我是借助了 afred 的 sdns插件，小小改造了下。

    dns.conf

    #
    # DNS Profiles
    #
    # Definition
    #   profile_name:dns_description:dns_servers
    #
    alibaba:Alibaba Public DNS:127.0.0.1,223.5.5.5,223.6.6.6
    v2ex:V2EX Public DNS:127.0.0.1,199.91.73.222,178.79.131.110
    114:114 Public DNS:127.0.0.1,114.114.114.114,114.114.115.115
    google:Google Public DNS:127.0.0.1,8.8.8.8,8.8.4.4
    openerdns:OpenerDNS:127.0.0.1,42.120.21.30

这样保证都会从本地dns来解析了

## dnsmasq.conf

    conf-dir=/usr/local/etc/dnsmasq.d

## smarthosts

    curl   "https://smarthosts.googlecode.com/svn/trunk/hosts" | tr -d '\r' | awk '{if($2 != "google.com" && $2 != "" && $1 !~ "#" && $2 !~ "dropbox") print "address=/"$2"/"$1}' > /usr/local/etc/dnsmasq.d/smarthost.conf
