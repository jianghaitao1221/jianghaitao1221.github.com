---

layout:     post
title:      "搭建DNS服务(九)"
subtitle:   "PowerDNS的递归服务器搭建"
date:       2018-7-25 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# PowerDNS Recursor

从4.1.0开始，递归从权威服务器移除，单独成为了一个服务。

## 安装

```bash
#Create the file '/etc/apt/sources.list.d/pdns.list' with this content:

deb [arch=amd64] http://repo.powerdns.com/ubuntu xenial-rec-41 main
#And this to '/etc/apt/preferences.d/pdns':

Package: pdns-*
Pin: origin repo.powerdns.com
Pin-Priority: 600
#and execute the following commands:

curl https://repo.powerdns.com/FD380FBB-pub.asc | sudo apt-key add - &&
sudo apt-get update &&
sudo apt-get install pdns-recursor
```

## 配置

### 删掉权威服务器中的递归配置

下面的参数已经废弃了。

```bash
allow-recursion
recursive-cache-ttl
recursor
```

### 修改权威服务器的端口

```bash
local-ipv6=
local-address=127.0.0.1
local-port=5300
```

### 配置递归服务器


```bash
sudo vi /etc/powerdns/recursor.conf

daemon=yes
local-address=0.0.0.0       #对应权威服务器的local-address和local-ipv6
allow-from=10.111.0.0/24    #对应权威服务器的allow-recursion 允许哪些ip进行递归
local-port=53               #对应权威服务器的local-port
forward-zones=private.example.com=127.0.0.1:5300  #哪些域名需要自己的权威服务器来解析，域名=权威服务器ip:端口
#forward-zones=local=127.0.0.1:5300 所有的TLD为local的访问自己的权威服务器
#forward-zones=example.org=203.0.113.210:5300;127.0.0.1, powerdns.com=127.0.0.1;198.51.100.10:530;[2001:DB8::1:3]:5300
#还可以配置forward-zones-file
forward-zones-recurse=.=8.8.8.8 #所有的请求发至8.8.8.8.除了forward-zones
```

## 相关资料

[recursor settings](https://doc.powerdns.com/recursor/settings.html)

[Migrating from using recursion on the Authoritative Server to using a Recursor](https://doc.powerdns.com/authoritative/guides/recursion.html)