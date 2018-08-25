---

layout:     post
title:      "搭建DNS服务(四)"
subtitle:   "coredns的安装和配置"
date:       2018-7-17 09:00:00
author:     "Jht"
header-img: "img/coredns-bg.png"
catalog: true
tags:
    - dns
    - coredns
---

# coredns

CoreDNS 是一个从Caddy中Fork出来的项目（同时继承了它的链式中间件风格），作为CNCF项目中的一员，它的目标是提供一个快速且灵活的DNS服务。

coredns被大家所熟知是因为从kubernetes1.9开始引入，作为kubernetes内部服务发现的默认dns。

## 安装

我当前的版本是 v1.2.0

### 可运行文件

[coredns 下载](https://github.com/coredns/coredns/releases)

### source

[source 下载](https://github.com/coredns/coredns/releases)

#### 安装go

查看怎么安装最新版本go

[go-wiki-Ubuntu](https://github.com/golang/go/wiki/Ubuntu)

```bsah
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt-get update
sudo apt-get install golang-go
```

#### 编译coredns

```bash
export GOPATH=${GOPATH-~/go}
mkdir -p $GOPATH/src/github.com/coredns
cd $GOPATH/src/github.com/coredns/
wget https://github.com/coredns/coredns/archive/v1.2.0.tar.gz
tar xvf v1.0.5.tar.gz
mv coredns-1.0.5 coredns
cd coredns
make CHECKS= godeps all

mv coredns /usr/local/bin
```

## 配置

```bash
sudo vi /etc/coredns/Corefile
####
. {
    whoami
    health
}

example.org {
    file /etc/coredns/zones/example.org
    prometheus
    errors stdout
    log stdout
    whoami
}
#=================================================
sudo vi /etc/coredns/zones/example.org
####
$ORIGIN example.org.
@       3600 IN SOA sns.dns.icann.org. noc.dns.icann.org. (
                                2017042745 ; serial
                                7200       ; refresh (2 hour
                                3600       ; retry (1 hour)
                                1209600    ; expire (2 weeks)
                                3600       ; minimum
                                )

    3600 IN NS a.iana-servers.net.
        3600 IN NS b.iana-servers.net.

www     IN A     127.0.0.1
        IN AAAA  ::1

tt      IN A     192.168.0.178
        IN AAAA  ::1
        IN TXT   HelloExampleTest
```

## 运行与测试

```bash
coredns -conf /etc/coredns/Corefile
dig www.example.org A @localhost
```

## 使用coredns作为我的dns服务器（像bind一样）

[CoreDNS as my dns server](https://github.com/coredns/coredns/issues/15)

## 添加 External Plugin

[添加 External Plugin](https://coredns.io/2017/07/25/compile-time-enabling-or-disabling-plugins/)

下载source，参见[编译coredns](#编译coredns)

```bash
sudo vi plugin.cfg
#添加
whoami:whoami
# External Plugin:External Plugin source,例子
reidis:github.com/hawell/redis

#
go get -u -v github.com/hawell/redis
go generate 
go build
coredns -plugins
```

## backend

### pdsql

像powerdns一样，使用sql，用的是sqlite，不要求扩展，不要求性能的可以用

[pdsql plugin](https://coredns.io/explugins/pdsql/)

### etcd

自带的plugin，etcd强一致性

[etcd plugin](https://coredns.io/plugins/etcd/)

### redis

不是很稳定，我没成功。redis 连接上就断，没日志。

`使用前一定先看版本，看提交记录，以下安装流程不一定适用`
`使用前一定先看版本，看提交记录，以下安装流程不一定适用`
`使用前一定先看版本，看提交记录，以下安装流程不一定适用`
重要的事说三遍。

工作流程：启动加载所有数据，查询是时候先查询缓存，没有去redis查询

生成数据和修改数据，用redis命令，没有更新机制，用ttl进行同步。

[redis plugin](https://coredns.io/explugins/redis/)

#### net因为被墙的安装方法

```bash
mkdir -p $GOPATH/src/golang.org/x/
cd $GOPATH/src/golang.org/x/
git clone https://github.com/golang/net.git net 
go install net
```

#### 解决bug-undefined: dnsutil.Dedup

目前版本有bug,解决办法[undefined: dnsutil.Dedup](https://github.com/arvancloud/redis/issues/3)

有另外一个人修改bug，下载他fork的版本，并且换到patch-1

```bash
mkdir -p $GOPATH/github.com/GotenXiao
git clone  https://github.com/GotenXiao/redis redis
go install net
```

## 缺点

- coredns还比较新，资料较少
- feature较少
- External Plugin质量和成熟度较低
- 没有web ui

## 总结

目前不能像powerdns一样用，可以像bind一样用。简单的dns需求可以
 
## 相关文档

[coredns github](https://github.com/coredns/coredns)

[官网](https://coredns.io)

