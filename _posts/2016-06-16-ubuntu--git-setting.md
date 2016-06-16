---
layout:     post
title:      "配置git"
subtitle:   " \"记住用户名和密码\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

##  用户名和邮箱

```bash
#用户名
git config --global user.name "USERNAME"
#邮箱
git config --global user.email "EMAIL"
```


## 保存凭证

```bash
#home目录下 windows是 C:\Documents and Settings\Administrator ，Linux是～/
vim .git-credentials
#然后输入
https://{username}:{password}@github.com #用你的用户名和密码替换
#保存文件
：wq
#添加config项
git config --global credential.helper store
```

## pull默认使用rebase

```bash
git config --global pull.rebase true
```


## 换行符

```bash
#windows
git config --global core.autocrlf true
#linux/mac
git config --global pull.rebase true
```