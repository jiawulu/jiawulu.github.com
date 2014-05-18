---
layout: post
title: "dns dig nslookup"
description: "dns 's basic knowledge and usage"
category: "tools"
tags: [dnsi,dig]
---
{% include JB/setup %}

## dns 

dns 是分层设计的，root 是中央服务器，每个dns地址的查询最初经过它，再由他解析一级域名，找到对应的下一级域名解析服务器（授权的过程），然后把具体要去解析域名的服务器地址返回给我们配置的dns服务器，然后做下一步询问过程。


![](http://yunpan.taobao.com/share/json/GetPhotoTag.do?info=93EjWHMHg&pInfo=A3YjLHQv&zoomSize=1000&app_name=)

## dig

dig 命令是最强大的dns分析工具，有它就够了，具体解读如下：

a. QUESTION SECTION：查询的内容
a. ANSWER SECTION：相应的内容，一般会得到至少一条A记录，否则就还没定义
a. AUTHORITY SECTION：授权信息
a. ADDITIONAL SECTION：每个授权服务器的IP地址
a. SERVER：查询的dns服务器，可能会被缓存

过程大体是：
1. 从SERVER查询www.aslibra.com，如果有有效缓存就返回了
2. 如果没有，则找到aslibra.com的授权服务器，下面有例子介绍
3. 从其中一个查询到结果(A记录)

### dig hostname

    ➜  ~  dig hws.m.taobao.com

    ; <<>> DiG 9.8.3-P1 <<>> hws.m.taobao.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 53140
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 0

    ;; QUESTION SECTION:    ## 询问内容
    ;hws.m.taobao.com.      IN  A

    ;; ANSWER SECTION:      ## 最终的应答结果，做了别名，最终ip是 42.156.219.4
    hws.m.taobao.com.   94  IN  CNAME   hws.m.split.taobao.com.
    hws.m.split.taobao.com. 199 IN  A   42.156.219.4

    ;; Query time: 83 msec
    ;; SERVER: 127.0.0.1#53(127.0.0.1)       ## `dns 服务器在本地（dnsmasq）`
    ;; WHEN: Sun May 18 09:24:45 2014
    ;; MSG SIZE  rcvd: 76

### dig hostname +trace

就是上图的的一个解析过程，如下：

    ➜  ~  dig hws.m.taobao.com +trace

    ; <<>> DiG 9.8.3-P1 <<>> hws.m.taobao.com +trace
    ;; global options: +cmd
    .           496015  IN  NS  e.root-servers.net.
    ...
    .           496015  IN  NS  h.root-servers.net.
    ;; Received 228 bytes from 127.0.0.1#53(127.0.0.1) in 533 ms

    com.            172800  IN  NS  j.gtld-servers.net.
    ... 
    com.            172800  IN  NS  k.gtld-servers.net.
    ;; Received 494 bytes from 192.112.36.4#53(192.112.36.4) in 313 ms

    taobao.com.     172800  IN  NS  ns4.taobao.com.
    taobao.com.     172800  IN  NS  ns5.taobao.com.
    taobao.com.     172800  IN  NS  ns6.taobao.com.
    taobao.com.     172800  IN  NS  ns7.taobao.com.
    ;; Received 186 bytes from 192.5.6.30#53(192.5.6.30) in 317 ms

    hws.m.taobao.com.   600 IN  CNAME   hws.m.split.taobao.com.
    split.taobao.com.   86400   IN  NS  splitns3.taobao.com.
    split.taobao.com.   86400   IN  NS  splitns2.taobao.com.
    split.taobao.com.   86400   IN  NS  splitns1.taobao.com.
    ;; Received 225 bytes from 110.75.38.29#53(110.75.38.29) in 34 ms

#### 最终的dns 解析落在 splitns1.taobao.com. 这组机器上，pe也只要维护该机器就好了

### dig -x ip

反查询ip对应的主机

    ➜  ~  dig -x 42.156.219.4

    ; <<>> DiG 9.8.3-P1 <<>> -x 42.156.219.4
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35744
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 0

    ;; QUESTION SECTION:
    ;4.219.156.42.in-addr.arpa. IN  PTR

    ;; AUTHORITY SECTION:
    219.156.42.in-addr.arpa. 600    IN  SOA hidden-master.aliyun.com. hostmaster.aliyun-inc.com. 2013090503 7200 900 2592000 600

    ;; Query time: 43 msec
    ;; SERVER: 127.0.0.1#53(127.0.0.1)
    ;; WHEN: Sun May 18 09:30:02 2014
    ;; MSG SIZE  rcvd: 125


## nslookup

    ➜  ~  nslookup hws.m.taobao.com
    Server:     127.0.0.1
    Address:    127.0.0.1#53

    Non-authoritative answer:
    hws.m.taobao.com    canonical name = hws.m.split.taobao.com.
    Name:   hws.m.split.taobao.com
    Address: 42.120.182.45

## host

    ➜  ~  host hws.m.taobao.com
    hws.m.taobao.com is an alias for hws.m.split.taobao.com.
    hws.m.split.taobao.com has address 42.120.182.45


## 参考

1. [dig理解DNS的解析过程](http://www.aslibra.com/blog/post/use_dig_dns_check.php)
2. [鸟哥的dns介绍](http://linux.vbird.org/linux_server/0350dns.php)
