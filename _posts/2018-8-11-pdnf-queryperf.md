---

layout:     post
title:      "搭建DNS服务(十)"
subtitle:   "PowerDNS的压力测试"
date:       2018-8-11 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# PowerDNS 压力测试

DNS服务器搭建好了，我们来压力测试一下。

## 测试工具

- queryperf （bind下的）
- dnsperf 

我们选用`queryperf`

## 安装

### 1.下载


```bash
wget https://www.isc.org/downloads/file/bind-9-12-2-p1/?version=tar-gz
#下载下来的文件名字不是个tar包
#index.html\?version\=tar-gz
#重命名
mv index.html\?version\=tar-gz bind.tar.gz
```

下载地址，[bind downlocads](https://www.isc.org/downloads/#)

## 2.编译

```bash
#step 1
tar -zxvf bind.tar.gz
#step 2
cd bind-9.12.2-P1/
cd contrib/queryperf/
ls
#============================= 
config.h.in  configure  configure.in  input  Makefile.in  missing  queryperf.c  README  utils
#=============================
#step 3
./configure  #可以./configure -h 查看帮助
#step 4
make
#step 5
sudo cp queryperf /usr/bin/
```

## 参数解释


- -d: 后面接上一个文件，文件的内容是用户对DNS的请求，一行为一条请求。发多少个查询就写多少条
- -s: DNS服务器地址
- -p: DNS服务器端口

更多参数：

```bash
queryperf -h

DNS Query Performance Testing Tool
Version: $Id: queryperf.c,v 1.12 2007/09/05 07:36:04 marka Exp $


Usage: queryperf [-d datafile] [-s server_addr] [-p port] [-q num_queries]
                 [-b bufsize] [-t timeout] [-n] [-l limit] [-f family] [-1]
                 [-i interval] [-r arraysize] [-u unit] [-H histfile]
                 [-T qps] [-e] [-D] [-R] [-c] [-v] [-h]
  -d specifies the input data file (default: stdin)
  -s sets the server to query (default: 127.0.0.1)
  -p sets the port on which to query the server (default: 53)
  -q specifies the maximum number of queries outstanding (default: 20)
  -t specifies the timeout for query completion in seconds (default: 5)
  -n causes configuration changes to be ignored
  -l specifies how a limit for how long to run tests in seconds (no default)
  -1 run through input only once (default: multiple iff limit given)
  -b set input/output buffer size in kilobytes (default: 32 k)
  -i specifies interval of intermediate outputs in seconds (default: 0=none)
  -f specify address family of DNS transport, inet or inet6 (default: any)
  -r set RTT statistics array size (default: 50000)
  -u set RTT statistics time unit in usec (default: 100)
  -H specifies RTT histogram data file (default: none)
  -T specify the target qps (default: 0=unspecified)
  -e enable EDNS 0
  -D set the DNSSEC OK bit (implies EDNS)
  -R disable recursion
  -c print the number of packets with each rcode
  -v verbose: report the RCODE of each response on stdout
  -h print this usage

```

## 测试

```bash
#step 1 test.txt
www.aa.local A
www.bb.local A
www.cc.local A
www.dd.local A
www.ee.local A
www.ff.local A
www.gg.local A
www.aa.local A
www.bb.local A
www.cc.local A
www.dd.local A
www.ee.local A
www.ff.local A
www.gg.local A
#step 2
queryperf -d test.txt -s 192.168.1.111
#step 3 result

DNS Query Performance Testing Tool
Version: $Id: queryperf.c,v 1.12 2007/09/05 07:36:04 marka Exp $

[Status] Processing input data
[Status] Sending queries (beginning with 192.168.1.111)
[Status] Testing complete

Statistics:

  Parse input file:     once
  Ended due to:         reaching end of file

  Queries sent:         5000 queries 发的的条数
  Queries completed:    5000 queries 匹配的条数
  Queries lost:         0 queries
  Queries delayed(?):   0 queries

  RTT max:              0.000782 sec
  RTT min:              0.000288 sec
  RTT average:          0.000438 sec
  RTT std deviation:    0.000046 sec
  RTT out of range:     0 queries

  Percentage completed: 100.00%
  Percentage lost:        0.00%

  Started at:           Fri Sep 14 10:31:19 2018
  Finished at:          Fri Sep 14 10:31:19 2018
  Ran for:              0.113270 seconds

  Queries per second:   44142.314823 qps

```