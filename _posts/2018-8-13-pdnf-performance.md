---

layout:     post
title:      "搭建DNS服务(十一)"
subtitle:   "PowerDNS的性能优化"
date:       2018-8-13 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# PowerDNS 优化

优化分两方面：

- PowerDNS authoritative
- PowerDNS recursor

## PowerDNS authoritative

### cache

- cache-ttl
 - Packet Cache的缓存秒数. Default: 20
- negquery-cache-ttl
 - 查不到（ non-existence）的缓存秒数. Default: 60
- query-cache-ttl
 - Query Cache的缓存秒数（The Query Cache caches these backend queries） Default: 20

以上三个缓存时间越长，性能越好，根据业务需求调整缓存时间。找到一个可以接受的最大值，进行设定。`例如60秒`。

### thread

- receiver-threads
  - 接受packets的线程。Default: 1
  - linux内核3.9之后支持SO_REUSEPORT，可以选择开启参数reuseport，数量应与cpu数量对应。
- distributor-threads
  - 每个receiver-thread有几个distributor-thread，distributor-thread复制链接数据库。Default: 3 （需注意mysql的最大连接数）

### log

- query-logging  （Default: no)
- log-dns-details （Default: no)
- log-dns-queries （Default: no)


### queue

- queue-limit （Default: 1500)

### 示例配置

```bash
cache-ttl=60
negquery-cache-ttl=60
query-cache-ttl=60
receiver-threads=2
reuseport=yes
distributor-threads=4
```

### MySQL

可以开启querycache。
调整最大连接数。

## PowerDNS recursor

### cache

- max-cache-entries
  - DNS缓存条数，每个线程1百万会满足大多数需求，Default: 1000000
  - 超过4百万以上优化效果不是很明显

### thread

- threads 
  - Default: 2
  - 线程数和cpu一样
  - 超过8不会有很大的改善
- pdns-distributes-queries
  - Default: yes
  - If set, PowerDNS will have only 1 thread listening on client sockets, and distribute work by itself over threads by using a hash of the query, maximizing the cache hit ratio
  - 4.2 会支持distributor-threads
- reuseport
  - Default: no
  - when pdns-distributes-queries is set to false and reuseport is enabled, every thread will open a separate listening socket to let the kernel distribute the incoming queries
-  cpu-map
  - Default: unset


### 示例配置

```bash
pdns-distributes-queries=no
reuseport=yes
```

## 优化结果

因为我只对我的私有域名进行测试。对12个A记录进行轮流查询，总共发送1万5000次。是否开启优化差别不大。
原因是因为就一开始的12个查库了，其余的查询都是在缓存中获取，这种情况下区别不是很大。

可以理解如果只是作为私有DNS服务，可查询的记录不是很多的情况下，性能问题不是瓶颈。

# 相关文档

[authoritative]()

[recursor]()

[使用socket so_reuseport提高服务端性能](http://xiaorui.cc/2015/12/02/%E4%BD%BF%E7%94%A8socket-so_reuseport%E6%8F%90%E9%AB%98%E6%9C%8D%E5%8A%A1%E7%AB%AF%E6%80%A7%E8%83%BD/)

[SO_REUSEPORT选项](https://www.jianshu.com/p/37216e299bff)

[Optimizing Negative Caching Time in DNS](https://securityblog.switch.ch/2016/05/02/optimizing-negative-caching-time-in-dns/)