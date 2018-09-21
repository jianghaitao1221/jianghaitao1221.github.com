---

layout:     post
title:      "搭建DNS服务(十三)"
subtitle:   "PowerDNS的管理命令（缓存管理）"
date:       2018-8-20 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# PowerDNS 管理命令

PowerDNS有很丰富的管理工具。

`但是我们在管理的过程中，不要使用操作命令，特别写操作的命令`，（缓存操作命令除外）。


## PowerDNS authoritative

PowerDNS authoritative有很多管理工具。

- pdns_control
- pdnsutil
- calidns
- dnsbulktest
- dnsgram
- dnspcap2calidns等

详见[Manual Pages](https://doc.powerdns.com/authoritative/manpages/index.html)

这里介绍最常用的两个

- pdns_control
- pdnsutil

### pdnsutil

pdnsutil非常强大，可以管理zone，管理DNSSEC，可以远程执行。

```bash
# 创建新zone并添加ns记录
pdnsutil create-zone example.com ns1.example.com
# 创建ns记录
pdnsutil add-record example.com @ NS 86400 ns1.example.com
# 创建ns1的A记录
pdnsutil add-record example.com ns1 A 3600 192.168.1.2
pdnsutil add-record bbdops.com $A A 3600 10.28.121.11
# 查看 zone
pdnsutil list-zone example.com
```

### pdns_control

```bash
# 显示缓存统计信息
pdns_control ccounts
# 清空指定域名的缓存
pdns_control purge example.net
# 清空全部域名的缓存
pdns_control purge
```

## PowerDNS recursor

- pdns_recursor
- rec_control

详见[Manual Pages](https://doc.powerdns.com/recursor/manpages/index.html)

### pdns_recursor

可以修改配置文件.

```bash
#设置local-address，allow-from，daemon
pdns_recursor --local-address=192.0.2.53 --allow-from=192.0.2.0/24 --daemon
```

### rec_control

rec_control可以查看和管理recursor

```bash
# 清空指定域名的缓存
# 添加$符号会删除example.net相关的所有。
# 删除example.net的records,negative records, packets
# 我试了rec_control wipe-cache example.net，但没任何删除

rec_control wipe-cache example.net$ 
# Emptying the cache overall
rec_control wipe-cache $
# 把现在所有的缓存输入到一个文件中
rec_control dump-cache /tmp/dns-cache
```

## 缓存操作

例:域名修改，但TTL尚未过期，想让修改生效。

- [authoritative cache control](#pdns_control)
- [recursor cache control](#rec_control)

