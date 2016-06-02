---
layout:     post
title:      "ubuntu16.04通过GUI设置开机启动应用"
subtitle:   " \"像windows一样设置启动项\""
date:       2016-06-02 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
    - linux
---

##  ubuntu16.04设置开机启动应用

在windows上有各种管家帮你管理开机启动项，`ububtu`上需要依赖`main menu`和`Startup Applications`两个工具来管理。


### 第一步 查找一个程序的运行命令

使用 `main menu`。

那么怎么找到`运行命令`
 
- 打开`Unity Dash`面板
- 搜索`main menu`
- 点击打开，会弹出包含所有应用的界面
- 找到你要的应用，选中
- 点击`Properties`,会弹出界面
- `Command`里的内容就是运行命令了


如果没有的话[安装地址](https://apps.ubuntu.com/cat/applications/saucy/alacarte/)
 

### 第二步 设置开机启动

- 打开`Unity Dash`面板
- 搜索`Startup Applications`
- 点击打开，会弹出包含所有启动项应用的界面
- 界面右边有`Add`,`Remove`,`Edit`
- 添加的话，点击`Add`，会弹出添加启动项的界面
- `Name`输入你要启动程序的名字
- `Command`输入运行命令（main menu找到的运行命令）
- `Comment`输入注释
- 点击`Add` 按钮