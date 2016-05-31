---
layout:     post
title:      "ubuntu16.04安装中文输入法"
subtitle:   " \"fcitx框架下安装\""
date:       2016-05-31 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
    - linux
---


## ubuntu16.04安装搜狗输入法

### 安裝 fcitx 輸入法框架

```
sudo add-apt-repository ppa:fcitx-team/nightly
sudo apt-get update
sudo apt-get install fcitx fcitx-config-gtk
#可选的输入法有  fcitx-sunpinyin fcitx-googlepinyin fcitx-module-cloudpinyin 等
#搜狗需要到官网下载deb

```
### 修改系统输入法

"System Settings"-->"Language Support"-->Keyboard input method sysytem-->fcitx

### 添加输入法

"Search your computy"-->"Fcitx Configuration"-->+

选择你要添加的人输入法，在输入状态下直接Ctrl + Space 即可调出拼音输入法。