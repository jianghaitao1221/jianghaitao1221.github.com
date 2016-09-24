---
layout:     post
title:      "jenkins使用saltstack plugin"
subtitle:   "\"saltstack学习四\""
date:       2016-09-12 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - ubuntu
---


## 安装saltstack plugin

- 登陆进入`jenkins`
- 点击`Manage Jenkins`
- 点击`Manage Plugins`
- 点击`Available`
- 找到`saltstack plugin`
- 安装并重启

## 使用saltstack plugin

### 添加Credentials

[用户名和密码](https://jianghaitao1221.github.io/2016/09/12/saltstack-ubuntu-3th/#为salt-api添加用户)

### 使用 

- `New Item`进入`Configure`页面
- 点击`Add build step`
- 选择`Send a message to Slat API`

![img](/img/in-post/salt-4th/saltstack-plugin.png)