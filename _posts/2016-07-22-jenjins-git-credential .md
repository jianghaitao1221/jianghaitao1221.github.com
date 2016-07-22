---
layout:     post
title:      "jenkins使用git凭证存储功能"
subtitle:   " \"不同操作系统对应的方法\""
date:       2016-07-22 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---


## Linux/Mac

```bat  
#缓存3600秒
git config --global credential.helper 'cache --timeout=3600'    
```

详见[git凭证存储和删除](https://jianghaitao1221.github.io/2016/07/22/git-credential/)



## windows

使用`git-credential-wincred.exe`，必须是`system`才起作用。

```bash
git config --system credential.helper wincred
```