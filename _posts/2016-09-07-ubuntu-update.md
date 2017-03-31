---
layout:     post
title:      "ubuntu升级系统"
subtitle:   "\"16.04升级到16.04.1\""
date:       2016-09-07 00:00:00
author:     "Jht"
header-img: "img/ubuntu-bg.jpg"
catalog: true
tags:
    - ubuntu
---

## 系统为LTS，并且未被废弃

### 升级一个版本 

```bash
apt-get update
apt-get dist-upgrade
```

### 升级多个版本 

修改`/etc/apt/source.list`  

```bash
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted #修改xenial为你要升级到的版本的代号
# deb-src http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted

```

执行更新

```bash
apt-get update
apt-get dist-upgrade
```
