---
layout:     post
title:      "git branch"
subtitle:   " \"保持fork出来的代码与被fork的代码一致\""
date:       2018-11-07 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

## git branch

本地已有`fork`出来的代码

```bash
#1.创建分支
git branch test
#2.切换分支
git checkcout test
#3.查看所有分支
git branch
#=======结果=======
  master
* test
#4.创建并且换
git checkout -b test
```