---

layout:     post
title:      "搭建DNS服务(六)"
subtitle:   "powerdns的安装和配置"
date:       2018-7-22 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# powerdns

PownerDNS发起于1999年，也是一个老牌的开源DNS了。它可以作为权威与递归DNS,很成熟，支持多种backend。有web前端。文档全面。

## 安装

官方提供了各个平台的安装方法，本文用的是 ubuntu 16.04。

其他的详见[installation](https://doc.powerdns.com/authoritative/installation.html)

### backebd

- BIND
- Generic Mysql
- Generic ODBC
- Generic Oracle
- Generic Postgresql
- Generic SQLite3
- GeoIP
- LDAP
- Lua2
- MyDNS
- OpenDBX
- Oracle
- Pipe
- Random
- Remote
- TinyDNS

powerdns支持很多的后端。具体支持到什么样子，详见[Backends](https://doc.powerdns.com/authoritative/backends/index.html)

### 安装mysql

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