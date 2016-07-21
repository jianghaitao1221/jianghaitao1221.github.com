---
layout:     post
title:      "ubuntu的compiz占用CPU过高"
subtitle:   " \"Xorg和chrome的CPU也过高\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
---

##  现象

我的主板坏了，换了个主板，发现一打开`chrome`就卡，就`top`了一下

```
 1719 aaaa+  20   0 1960376 226344  93812 S  150.2  1.4   6:51.53 compiz                          
  988 root   20   0  436776 176068  99352 R  60.3  1.1   6:54.80 Xorg  
 5207 aaaa+  20   0 1050244 183492  65920 S  40.5  1.1   2:52.70 chrome                                                   
 2711 aaaa+  20   0  863832 112924  51632 S  10.3  0.7   4:26.22 chrome                          
 5528 aaaa+  20   0 2829464  73676  35704 S   8.6  0.5   2:47.92 RTX.exe                         
 5580 aaaa+  20   0 2747556 152516  84696 S   6.3  0.9   2:28.06 QQ.exe                          
 5500 aaaa+  20   0   46680  11708   2976 S   4.3  0.1   1:21.87 wineserv
```

发现`compiz，Xorg，chrome`这三个货造成的

我把`chrome`关掉正常了

## 造成问题的原因

```bash
#运行
glxinfo | grep render
#显示如下
direct rendering: Yes
OpenGL renderer string: Software Rasterizer
```

这是因为系统拿`CPU`去帮`GPU`去了，为什么帮`GPU`呢，是因为系统没有用显卡去工作，而是用软件渲染。所以`CPU`去高了，
应该是我换主板导致显卡驱动丢失。

## 解决方法

- 打开System Settings
- 点击Software and updates 
- 选择Additional drivers
- 选择你的显卡驱动(而不是X.Org X server)
- 重启