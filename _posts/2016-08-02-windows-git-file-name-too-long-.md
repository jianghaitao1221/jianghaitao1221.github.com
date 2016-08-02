---
layout:     post
title:      "windows上git报文件名字过长的错误"
subtitle:   " \"error: unable to create file (Filename too long)\""
date:       2016-08-02 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---


## windows

`git`克隆一个库，报错说`error: unable to create file "xxxxxxxxxxxx-xxxxx-xxxxxxx.txt" (Filename too long)`

## 解决办法

```bash
git config --system core.longpaths true
```