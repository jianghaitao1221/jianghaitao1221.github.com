---
layout:     post
title:      "ubuntu安装maven"
subtitle:   "\"版本3.3.9\""
date:       2016-09-06 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - maven
    - ubuntu
   
---

## 安装

```bash
sudo apt-get purge maven maven2 maven3
sudo apt-add-repository ppa:andrei-pozolotin/maven3
sudo apt-get update
sudo apt-get install maven3
```

## 配置M2_HOME

```bash
dpkg -L mvn #显示安装目录
#配置环境变量
export M2_HOME=/usr/share/maven
```