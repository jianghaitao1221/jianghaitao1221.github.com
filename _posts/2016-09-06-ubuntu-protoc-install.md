---
layout:     post
title:      "ubuntu安装protobuf"
subtitle:   "\"版本3.3.9\""
date:       2016-09-06 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - protobuf
    - ubuntu
---

## protobuf地址

[github地址](https://github.com/google/protobuf)

## 下载地址

[下载地址](https://github.com/google/protobuf/releases)

## 安装

### 依赖

```bash
sudo apt-get install autoconf automake libtool curl make g++ unzip
```

#### install

```bash
./configure
make
make check
sudo make install
sudo ldconfig # refresh shared library cache.
```

### 官方文档

[安装文档](https://github.com/google/protobuf/blob/master/src/README.md)