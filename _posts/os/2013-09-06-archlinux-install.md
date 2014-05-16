---
layout: post
title: "archlinux install tutorial"
description: ""
category: "linux"
tags: [archlinux,linux]
---
{% include JB/setup %}

## network

### ipconfig,ping
    pacman -S iputils net-tools

### dig
    pacman -S dnsutils

### dnsmasq

    [dnsmasq](https://wiki.archlinux.org/index.php/Dnsmasq)
    
    pacman -S dnsmasq

    systemctl enable dnsmasq

    echo "nameserver 127.0.0.1" > /etc/resolv.conf.head

### 开机启动

    systemctl enable dhcpcd@eth0|其他端口

### 重启network

    systemctl restart dhcpcd

### dyndns
    
    /etc/systemd/system/freedns.service

    ➜  ~  more freedns.service 
    [Unit]
    Description=Dyndns Service
    After=network.target

    [Service]
    Type=forking
    User=nobody
    ExecStart=/usr/bin/freedns
    Restart=on-abort

    [Install]
    WantedBy=multi-user.target

#### 查看日志

    journalctl -u freedns


## shell

### zsh

    pacman -S git zsh

[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh#the-manual-way)


## 字体

    ttf-microsoft-yahei
    ttf-ubuntu-font-family

    /usr/share/font/
    $ mkfontdir
    $ mkfontscale
    $ fc-cache -fv

## 输入法

    $ yaourt fcitx-cloudpinyin
    $ yaourt kcm-fcitx
    

## 小工具

1. lrzsz


## theme

    $ yaourt kfaen
    $ sudo pacman -S faience-icon-theme
    $ yaourt caledonia
    $ yaourt chromi-kde4decor-git

## pacman

    # rankmirrors -n 6 mirrorlist.backup > mirrorlist
    # pacman -Syy
    
