---

layout:     post
title:      "搭建DNS服务(八)"
subtitle:   "PowerDNS的master/slave设置"
date:       2018-7-23 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# PowerDNS支持的模式

- master/slave
- native replication(数据库复制)

## one database

`假设我们的DNS服务器是一个私有DNS，或者不跨国家和区域，只服务与内部。`
`而database又高可用，有高性能，那么两个native模式的DNS服务器连接一个数据库的也是可行的。`

## native replication

官方的描述是默认就支持，不需要额外的设置，native replication模式不会发送和响应更新通知。
只要配置mysql或oracle的主从复制就可以了。

为什么复制数据库就可以？和他的机制有关。详见()

## master/slave


```bash
sudo apt-get install mysql-server mysql-client
```

### 安装powerdns


```bash
#Create the file '/etc/apt/sources.list.d/pdns.list' with this content:
deb [arch=amd64] http://repo.powerdns.com/ubuntu xenial-auth-41 main
#And this to '/etc/apt/preferences.d/pdns':
Package: pdns-*
Pin: origin repo.powerdns.com
Pin-Priority: 600
and execute the following commands:
#运行
curl https://repo.powerdns.com/FD380FBB-pub.asc | sudo apt-key add - &&
sudo apt-get update &&
sudo apt-get install pdns-server
#安装mysql的backend
sudo apt-get install pdns-backend-mysql #会提醒你是否初始化pdns的数据库
```

### 配置

#### 数据库

安装`pdns-backend-mysql`时会创建`/etc/powerdns/pdns.d/pdns.local.gmysql.conf`。
删除`/etc/powerdns/pdns.d下`其余的配置。
修改`pdns.local.gmysql.conf`。

```bash
sudo vi pdns.local.gmysql.conf

launch+=gmysql

gmysql-host=localhost
gmysql-port=
gmysql-dbname=pdns
gmysql-user=your user
gmysql-password=your password
gmysql-dnssec=yes

sudo service pds restart

```

#### 测试

测试结果如下：

```bash
dig @localhost

; <<>> DiG 9.10.3-P4-Ubuntu <<>> @localhost
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 50124
;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1680
;; QUESTION SECTION:
;.                              IN      NS

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1)
;; WHEN: Mon Aug 27 10:55:51 CST 2018
;; MSG SIZE  rcvd: 28


```