---
layout:     post
title:      "ubuntu循环登陆问题"
subtitle:   "\"分辨率变低了\""
date:       2016-09-07 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
---

## 现象

能进入`ubuntu`图形界面，并且在登录界面输入正确的密码后，系统闪一下进入黑屏，然后快速进入一串命令行，然后又进入登录界面要求输入密码。如此不断循环登录，却始终无法登入桌面。

## 查看

`Ctrl+Alt+F1`进入`tty1`,输入用户名和密码。查看`～/.xsession-errors`.

## 原因

我是从`16.04`升级到`16.04.1`，完了就登陆不进去了，登陆界面分辨率变低了。这就是显卡出了问题。

## 解决办法

### 第一步 卸载显卡驱动

进入`tty1`

```bash
#我的显卡是N卡
sudo apt-get remove --purge 'nvidia-*'
```

### 第二步 重启

这时候重启就能进入系统了

#### 如果Launcher / Top Panel没了（界面边框不见了）

[详见](http://jianghaitao1221.github.io/2016/05/31/ubuntu-after-remove_ibus/)

### 第三步 安装N卡驱动

最简单的方法：

- system setting
- software & updates
- additional drivers
- 就能看见你的显卡驱动版本了

```bash
sudo apt-get install nvidia-361
```

安装的时候有肯呢个会设置密码来启用驱动，重启验证就行了


查看现在采用的驱动

```bash
prime-select query
#显示nvidia 就成功了
```

没有的话

- system setting
- software & updates
- additional drivers
- 选择N卡驱动

### 重启

问题解决。