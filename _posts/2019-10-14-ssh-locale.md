---
layout:     post
title:      "ssh乱码"
subtitle:   " \"ssh与locale\""
date:       2019-10-14 03:00:00
author:     "Jht"
header-img: "img/post-bg-linux.jpg"
catalog: true
tags:
    - linux
    - ssh
---

## ssh乱码

ssh成功后，执行命令，控制台输出会乱码。

### 这是为啥？

这个会出现在centos7最小化安装上。原因centos7的编码和你client端的编码不一致。
使用locale查看，例：


```bash
[远程机器]$locale
# 结果
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
#=======================================
[本地机器]$locale
LANG=C.UTF-8
LC_CTYPE="C.UTF-8"
LC_NUMERIC="C.UTF-8"
LC_TIME="C.UTF-8"
LC_COLLATE="C.UTF-8"
LC_MONETARY="C.UTF-8"
LC_MESSAGES="C.UTF-8"
LC_PAPER="C.UTF-8"
LC_NAME="C.UTF-8"
LC_ADDRESS="C.UTF-8"
LC_TELEPHONE="C.UTF-8"
LC_MEASUREMENT="C.UTF-8"
LC_IDENTIFICATION="C.UTF-8"
LC_ALL=

```

### 原因

ssh时会发送本地的环境信息到远端。

```bash
ssh -v username@ip 

#结果
OpenSSH_7.6p1 Ubuntu-4, OpenSSL 1.0.2n  7 Dec 2017
debug1: Reading configuration data /etc/ssh/ssh_config
.......略
 
debug1: Sending environment. 
debug1: Sending env LC_MESSAGES = en_US.UTF-8 
debug1: Sending env LC_COLLATE = en_US.UTF-8 
debug1: Sending env LANG = en_US.UTF-8 
debug1: Sending env LC_CTYPE = en_US.UTF-8 
.......略
```

### locale

locale有12个属性，即 12个LC_*。还有两个变量：LC_ALL和LANG。
优先级： LC_ALL>LC_*>LANG , LC_ALL是最上级设定或者强制设定，而LANG是默认设定值。

#### 相关命令

```bash

localectl --help
localectl [OPTIONS...] COMMAND ...

Query or change system locale and keyboard settings.

  -h --help                Show this help
     --version             Show package version
     --no-pager            Do not pipe output into a pager
     --no-ask-password     Do not prompt for password
  -H --host=[USER@]HOST    Operate on remote host
  -M --machine=CONTAINER   Operate on local container
     --no-convert          Dont convert keyboard mappings

Commands:
  status                   Show current locale settings
  set-locale LOCALE...     Set system locale
  list-locales             Show known locales
  set-keymap MAP [MAP]     Set console and X11 keyboard mappings
  list-keymaps             Show known virtual console keyboard mappings
  set-x11-keymap LAYOUT [MODEL [VARIANT [OPTIONS]]]
                           Set X11 and console keyboard mappings
  list-x11-keymap-models   Show known X11 keyboard mapping models
  list-x11-keymap-layouts  Show known X11 keyboard mapping layouts
  list-x11-keymap-variants [LAYOUT]
                           Show known X11 keyboard mapping variants
  list-x11-keymap-options  Show known X11 keyboard mapping options

```

#### 设置locale

```bash
# 设置
sudo localectl set-locale  LANG=en_US.utf8
# ssh client（本地机器）添加到.bashrc
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```