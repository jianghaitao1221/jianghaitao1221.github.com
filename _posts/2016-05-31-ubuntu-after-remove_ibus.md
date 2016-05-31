---
layout:     post
title:      "ubuntu 16.04恢复卸载ibus后带来的一系列问题"
subtitle:   " \"恢复Appearance、Text Entry、Launcher 、 Top Panel等\""
date:       2016-05-31 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
    - linux
---

## ubuntu16.04恢复卸载ibus后带来的一系列问题

由于之前安装QQ和RTX不能输入中文。发现是ibus的问题。我就把ibus卸载了。结果***`悲剧了`***。

- 系统设置里好多功能不见了（Appearance、Text Entry等）
- Launcher / Top Panel没了（界面边框不见了） 

因为卸载的时候卸载掉了 一些依赖。

### 修复

```
sudo rm -fr ~/.cache/compizconfig-1 sudo rm -fr ~/.compiz
sudo rm -fr ~/.Xauthority sudo rm -fr ~/.config/autostart
sudo apt-get install --reinstall ubuntu-desktop unity compizconfig-settings-manager upstart
sudo dconf reset -f /org/compiz/
setsid unity

```
