---
layout:     post
title:      "Hash Sum mismatch"
subtitle:   "\"apt-get update失败\""
date:       2017-2-23 08:00:00
author:     "Jht"
header-img: "img//home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
---

## 情景

### 安装gcc-6 g++-6 libstdc++6

```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update

```

### 报错

```bash
W: Failed to fetch http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu/dists/trusty/main/binary-amd64/Packages  Hash Sum mismatch

E: Some index files failed to download. They have been ignored, or old ones used instead.

```

## 解决问题

### 第一步

```bash
sudo rm -rf /var/lib/apt/lists/*
```

### 第二部


```bash
#更新指定的库

sudo apt-get update -o Dir::Etc::sourcelist=/etc/apt/sources.list.d/ubuntu-toolchain-r-test-trusty.list -o Dir::Etc::sourceparts="-" -o APT::Get::List-Cleanup="0"

#加上Debug

 -o Debug::Acquire::http=true -o Debug::pkgAcquire::Auth=true -o Debug::Hashs=true

 #完整的

sudo apt-get update -o Dir::Etc::sourcelist=/etc/apt/sources.list.d/ubuntu-toolchain-r-test-trusty.list -o Dir::Etc::sourceparts="-" -o APT::Get::List-Cleanup="0" -o Debug::Acquire::http=true -o Debug::pkgAcquire::Auth=true -o Debug::Hashs=true

#日志
201 URI Done: gzip:/var/lib/apt/lists/partial/ppa.launchpad.net_ubuntu-toolchain-r_test_ubuntu_dists_trusty_main_binary-amd64_Packages
RecivedHash: SHA256:ea0f64b188cd752f3cb76bbf5cdbe48c4c57badeb895f5baf27424c88871340c
ExpectedHash: SHA256:cb89f35f448139f2c6d3cb40cad7fa5a163adf5bda066225162d51d0d723d954

HTTP/1.1 302 Found
Cache-Control: no-cache
Location: http://120.52.72.23:80/ppa.launchpad.net/c3pr90ntc0td/ubuntu-toolchain-r/test/ubuntu/dists/trusty/main/binary-amd64/Packages.gz
Content-Length: 0
Date: Mon, 10 Oct 2016 09:06:59 GMT
Via: 1.1 pek7-proxy-2.amazon.com:80 (Cisco-WSA/9.0.1-162)
Connection: keep-alive

#看一下我们下载下来的东东

file /var/lib/apt/lists/partial/ppa.launchpad.net_ubuntu-toolchain-r_test_ubuntu_dists_trusty_main_binary-amd64_Package

#结果

/var/lib/apt/lists/partial/ppa.launchpad.net_ubuntu-toolchain-r_test_ubuntu_dists_trusty_main_binary-amd64_Packages: bzip2 compressed data, block size = 900k

他是个.bzip2不是个.gz

## 加上命令

sudo rm -rf /var/lib/apt/lists/* 

sudo apt-get update -o Dir::Etc::sourcelist=/etc/apt/sources.list.d/ubuntu-toolchain-r-test-trusty.list -o Dir::Etc::sourceparts="-" -o APT::Get::List-Cleanup="0" -o Acquire::CompressionTypes::Order::=gz


```


