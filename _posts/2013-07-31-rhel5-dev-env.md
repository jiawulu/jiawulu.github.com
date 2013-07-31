---
layout: post
title: "rhel5 dev env"
description: ""
category: "linux"
tags: [rhel,linux,ruby]
---
{% include JB/setup %}

## 切换到centos

1. 卸载yum相关的包

        rpm -qa|grep yum
        sudo rpm -e --nodeps yum


2. 下载yum相关包

        wget http://mirror.centos.org/centos/5/os/x86_64/CentOS/python-iniparse-0.2.3-6.el5.noarch.rpm
        wget http://mirror.centos.org/centos/5/os/x86_64/CentOS/yum-3.2.22-40.el5.centos.noarch.rpm
        wget http://mirror.centos.org/centos/5/os/x86_64/CentOS/yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm
        wget http://mirror.centos.org/centos/5/os/x86_64/CentOS/yum-metadata-parser-1.1.2-4.el5.x86_64.rpm

3. 安装

        sudo rpm -ivh python-iniparse-0.2.3-6.el5.noarch.rpm
        sudo rpm -ivh yum-3.2.22-40.el5.centos.noarch.rpm yum-metadata-parser-1.1.2-4.el5.x86_64.rpm yum-fastestmirror-1.1.16-21.el5.centos.noarch.rpm

4. 清理

        cd /etc/yum
        vi pluginconf.d
        sudo vi /etc/yum.conf
            plugins=0
        cd /var/cache
        sudo rm -rf yum

5. 增加epel

        sudo rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm

6. 更新

        sudo yum clean all
        sudo yum -y update

