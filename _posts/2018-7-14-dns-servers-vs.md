---

layout:     post
title:      "搭建DNS服务(二)"
subtitle:   "DNS Server选型与分析"
date:       2018-7-14 19:00:00
author:     "Jht"
header-img: "img/dns-bg.png"
catalog: true
tags:
    - dns
---

# 开源的DNS Server

开源的dns有很多。

- Bind
- PowerDNS
- CoredDNS
- DNSPod-SR
- Dnsmasq
- Atomia DNS

我们主要介绍Bind，PowerDNS，CoredDNS，这三个成熟，活跃，在不断的修改或增加新的功能。

## Bind

Bind一直以来基本上都是DNS的工业标准，1984年，加州大学伯克利分校的几个学生做的，名字叫做Berkeley Internet Name Domain（BIND）。
Bind9是ISC开发人员对Bind重写。

Bind9可以作为权威与递归DNS。

### 权威DNS服务

- RRL 减少放大攻击
- DLZ 支持从外部数据库获取Zone数据，但不建议在high-query的环境中使用。
- Minimum Re-load Time
- HSM Support HSM加密
- DNSSEC with In-line Signing
- Catalog Zones zone的增加删除更加方便，在一个db file中定义等多个SOA
- Scalable Master/Slave Hierarchy


### 递归DNS

- NXDOMAIN Redirect 当查询域名不存在时，转向一个Web页面，它依赖于DLZ特征。
- Flexible Cache Controls 对于不正确或过期的域名记录，可以干掉他们。
- Split DNS 通过View和可见性隔离DNS信息。
- Optimum Cache Hit Rate 通过 DNS prefetch ，来提高命中率。
- Resolver rate-limiting 在遭受攻击时，对权威DNS查询限速，减轻DDoS攻击影响。
- DNSSEC Validation
- GeoIP 根据查询者IP的地理位置给出不同的DNS结果
- RPZ Response Policy Zones, DNS防火墙，重写一些域名响应，指向NXDOMAIN等。

### 性能

官方给出的性能测试指标是500k左右，这个是没有任何影响因数的，没有log等。

加了日志一般会缩减，一般会缩减5到7倍。如果日志更复杂可能会更慢。

测试环境4核CPU，4G内存为70K。

加了DLZ就更慢一些，会下降10倍甚至更多。

bind安装目录下`contrib/queryperf`是性能测试模块，需编译安装。

### 监控

通过配置Statistics,感觉比较弱。只有一些统计信息。

### web前端

以下前端都是基于zone file的：

- namedmanager 比较老，php7不兼容，不支持master/salve
- GloboDNS 配置较复杂

### Bind相关文档

[官方网站](https://www.isc.org/)

[bind-features](https://www.isc.org/downloads/bind/bind-features/)

[BIND 9.10 – Statistics, Troubleshooting and Zone Configuration](https://www.isc.org/blogs/bind-9-10-statistics-troubleshooting-and-zone-configuration/)

#### 性能测试

[性能测试](https://www.isc.org/blogs/bind9-performance-history/)

#### web前端相关

[namedmanager](https://github.com/jethrocarr/namedmanager)

[GloboDNS](https://github.com/globocom/GloboDNS)

#### 监控相关

[cacti](https://www.cacti.net/)

## PowerDNS

PownerDNS发起于1999年，2015成为了Open-Xchange的一部分，。它可以作为权威与递归DNS,很成熟，支持多种backend。有web前端。文档全面。应用最为广泛。

- PowerDNS-Authoritative 权威DNS服务
- PowerDNS-Recursor 递归DNS服务
- dnsdist 针对DoS攻击的动态DNS负载均衡器

#### 所有PowerDNS产品共有的features

- IPv4, UDP/TCP
- IPv6, UDP/TCP, 100% compliant
- Remotely pollable statistics for real time graphing
- High performance
- SNMP statistics bridge (read only)


#### PowerDNS-Authoritative

- MySQL, PostgreSQL, Oracle, Sybase, Microsoft SQL Server, LDAP, SQLite3
  - Including replication
- Near instant start up time
- Plain BIND zone files
  - Brief start up time
- Migration tools from legacy DNS platforms (zone2sql，bind迁移)
- Internal Lua-based scripted answer generation
- External high-performance Script-based answer generation
- Geographical load balancing
- Full DNSSEC support including all standardized algorithms
- TSIG for transaction signatures, AXFR authorization/requests
- Master/Slave support
- Built-in web server for statistics and limited direct control
- API for direct control (pdns_control, pdnssec)
- Local and remote access

#### PowerDNS-Recursor

- Full support for all relevant standards
- Advanced anti-spoofing measures
- Reconfiguration without downtime
- Plain BIND zone files for “resolved hosting”
- Internal Lua-based scripted answer generation
- Question interception, answer reconditioning, NXDOMAIN redirection
  - Including ‘block lists’ and security measures
- API for direct control (rec_control)
  - Local and remote access
- DNS Response Policy Zones (RPZ)
- DNS64

#### dnsdist

- Dynamically route queries to backend servers
- Advanced anti-spoofing measures
- Reconfiguration without downtime
- Kernel based filtering of harmful traffic, rejecting packets at 'line speed'
- Internal Lua-based scripted answer generation
- Question interception, answer reconditioning, NXDOMAIN redirection
  - Including ‘block lists’ and security measures
- Built-in memory efficient cache for increased performance
- Ability to continue serving data from cache for non-responsive backends
- Smart rate limiting per user, per subnet, per domain
- Capable of writing dynamic rules to block harmful traffic

### 性能

能处理成千上万. A quad Xeon 3GHz has been measured functioning very well at 400000 real life replayed packets per second.

### github使用powerdns

[dns-infrastructure-at-github](https://githubengineering.com/dns-infrastructure-at-github/)

### 监控

Powerdns内置了详细的性能指标项，包括Metrics and Statistics。我们可以非常容易的去采集这些指标，然后推送给我们的监控系统。

### web前端

web前端比较多，下面列出几个作为代表：

- PowerDNS-Admin
- Opera DNS UI
- DjangoPowerDNS
- NicTool

### 相关文档

[官网](https://www.powerdns.com/)

[PowerDNS-Authoritative](https://www.powerdns.com/auth.html)

[PowerDNS-Recursor](https://www.powerdns.com/dnsdist.html)

[官方文档](https://doc.powerdns.com/authoritative/index.html)

[github](https://github.com/PowerDNS/pdns)

#### 监控相关

[PowerDNS-Authoritative Performance](https://docs.powerdns.com/authoritative/performance.html)

[PowerDNS-Recursor Metrics](https://docs.powerdns.com/recursor/metrics.html)

## CoreDNS

CoreDNS 是一个从Caddy中Fork出来的项目（同时继承了它的链式中间件风格），作为CNCF项目中的一员，它的目标是提供一个快速且灵活的DNS服务。

coredns被大家所熟知是因为从kubernetes1.9开始引入，作为kubernetes内部服务发现的默认dns。


### features

CoreDNS同样可以权威与递归DNS，因为他是链式的，所有的插件都是可插拔的。

他目前有plugin 34个。External Plugins若干。

但是External Plugins的质量不是很高。单独做为dns服务器的相关生态不完善。

但是他有的好爹好妈，估计未来发展前景会很好。

### 性能 

我没跑起来，没测到。他自带测试性能的模块。下面有使用链接。

### 监控

自带支持prometheus，在配置文件开启就行。

### web前端

单独作为dns服务器，还没有相关的web前端。

### 相关文档

[官网](https://www.colabug.com/1330528.html)

[github](https://github.com/coredns/coredns)

### 性能测试

[性能测试](https://coredns.io/2017/08/08/coredns-performance-testing/)

## 其他的DNS服务器

其他的几个DNS服务器，不是很活跃，有的github好久都没更新了。就没仔细看了

- dnspod-sr是DNSPod官方开源的一款递归DNS服务器软件。比Bind9快一倍左右。
- Dnsmasq 提供 DNS 缓存和 DHCP 服务功能。作为域名解析服务器(DNS)，，dnsmasq可以通过缓存 DNS 请求来提高对访问过的网址的连接速度。作为DHCP 服务器，dnsmasq 可以用于为局域网电脑分配内网ip地址和提供路由。DNS和DHCP两个功能可以同时或分别单独实现。dnsmasq轻量且易配置，适用于个人用户或少于50台主机的网络。
- Atomia DNS是一个开源的、免费的，多租户DNS管理系统，易于使用，可靠，可扩展，通过编程接口处理大量的DNS数据。Atomia DNS还包含同步代理确保该数据命中所有的dns服务器。推荐PowerDNS和BIND-DLZ DNS服务器，PowerDNS是默认代理选项。

#### 相关文档

##### DNSPod-SR

[DNSPod-SR github](https://github.com/DNSPod/dnspod-sr)

##### Dnsmasq

[Dnsmasq github](https://github.com/infinet/dnsmasq)

[Dnsmasq github](https://wiki.archlinux.org/index.php/Dnsmasq_(简体中文))

[官网](http://www.thekelleys.org.uk/)

##### Atomia DNS

[官网](http://atomiadns.com/)

[Atomia DNS github](https://github.com/atomia/atomiadns/)

[演示地址](http://atomiadns.net/login/%2F)

[安装指南](http://atomiadns.com/get-started/install-atomia-dns/)

# 选型

## 什么样的DNS服务器才算好的服务器？

下面仅仅是一家之见，大家可以交流。

- 智能化
- 可扩展
- 高可用
 - 数据持久化
- 安全
- 规范化
  - 规范化部署
  - 规范化监控
  - 规范化管理


### 智能化

智能解析，像CDN一样根据用户距离，负载情况等情况选择DNS Server节点。来改善访问速度。

IP智能检测并制止来自与恶意活动有关的IP地址进行的任何访问保护基础架构的安全性。

### 可扩展

DNS服务扩展。

### 高可用

DNS集群服务，解决单点DNS服务故障。

#### 数据持久化

数据要持久化，数据存入数据库，数据库高可用。

### 安全

包括DNS Blacklist、DNSSEC、RRL（Response Rate Limiting），以及RPZ（Response Policy Zones）等。

### 规范化

#### 规范化部署

master提供记录更新、所有服务slaves只负责同步。一切以master为准。

如果怕操作web前端，影响数据库性能，那么master值提供修改，不提供服务，slaves提供服务。

#### 规范化监控

提供统计,性能分析，各种指标。以方便对DNS服务进突分析、健康性检查和监控。

#### 规范化管理

可以通过API和web前端对记录进行操作。减少误操作带来的损失。


## 符合需求的才是最好的

上一节列出了一堆需求，如果你只是在vpc中搭建private DNS server，
name有的需求就用不上，比如智能化。

## DNS Server 对比

### 需求对比

| DNS Server | 智能化               |可扩展 | 高可用 | 数据持久化                   | 安全|规范化部署|规范化监控|规范化管理|
| ---------- | ---------------------| ---- |------ |------------------------     |-----|-------- |-------- |-------- |
| Bind       | 否                   | 是   |是      |是(性能差)                   |是   |是        |是(指标少)|无API，第三方的直接管理文件(部署复杂)|
| PowerDNS   | 是                   | 是   |是      |是                          |是   |是       |是        |是       |
| CoreDNS    | 是(仅支持round_robin)| 是    |是      |是(官方etcd，第三方的成熟度低)|是   |是       |是        |无(好像有人在开发) |


## 结论

目前来看PowerDNS生态最好，用的人最多，文档全面，功能最全，而且在持续开发中，不用担心维护问题，性能最好，是搭建DNS服务器的首选。

