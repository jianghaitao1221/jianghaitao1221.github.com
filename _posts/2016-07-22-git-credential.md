---
layout:     post
title:      "git凭证存储和删除"
subtitle:   " \"不同操作系统对应的方法\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

# 保存凭证

## Linux

```bat
#将凭证用明文的形式存放在磁盘中
git config --global credential.helper store  
#配置到缓存 默认15分钟  
git config --global credential.helper cache   
#修改缓存时间  
git config --global credential.helper 'cache --timeout=3600'    
```

## Mac

除了`Linux`中提到的方式，还可以用`osxkeychain`，凭证缓存到你系统用户的钥匙串中。 
这种方式将凭证存放在磁盘中，并且永不过期，但是是被加密的，这种加密方式与存放 HTTPS 凭证以及 Safari 的自动填写是相同的。


## windows

比较蛋疼，上面的都用不了

### git-credential-wincred.exe

git自带的

```bash 
git config --global credential.helper wincred
```

### git-credential-winstore

下载一个[git-credential-winstore](https://github.com/anurse/git-credential-winstore/downloads)，运行完，在global的配置文件里会写入配置。

```bash 
#文件内容如在
 [credential]
	helper = !'C:\\Users\\xxx\\AppData\\Roaming\\GitCredStore\\git-credential-winstore.exe'
```

### Git-Credential-Manager-for-Windows

下载一个最新版的[Git-Credential-Manager-for-Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)，运行`install.cmd`，在global的配置文件里会写入配置。

# 删除凭证

## 禁止使用缓存凭据

```bash
#如果重新启用的话， 还将继续拥有其存储库中可用的缓存的凭据
git config --global --unset credential.helper 
```

## wincred

- 打开控制面板
- 点击凭据管理器
- 就能看见凭据了
