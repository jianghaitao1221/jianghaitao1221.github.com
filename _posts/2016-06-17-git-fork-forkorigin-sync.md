---
layout:     post
title:      "git的fork与被fork的仓库同步"
subtitle:   " \"保持fork出来的代码与被fork的代码一致 and 自己的master到branch\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

###  同步之前

本地已有`fork`出来的代码

```bash
git clone "fork出的仓库"
```

### 添加"被fork的仓库"的远程分支

```bash
#上游名称自己起
git remote add "上游名称" "被fork的仓库"
```

### 查看本地分支

```bash
git remote -v 
```

显示如下：

```bash
"上游名称"  "被fork的仓库" (fetch) #ohydra	https://github.com/xxxx/xxxx.git (fetch)
"上游名称"	"被fork的仓库" (push)  #ohydra	https://github.com/xxxx/xxxx.git (push)
origin	"fork出的仓库" (fetch)    #origin	https://github.com/yyyyy/xxxx.git (fetch)
origin	"fork出的仓库" (push)     #origin	https://github.com/yyyyy/xxxx.git (fetch)

```

### fetch"被fork的仓库"代码到本地

```bash
git fetch "上游名称"
```

### 切换到"fork出的仓库"的master分支

```bash
git checkout master
```

### 合并"被fork的仓库"到master分支

```bash
git merge "上游名称"/master
```


### fetch和merge合并

```bash
git pull upstream master
```

### push到"fork出的仓库"

```bash
git push origin master
```

### master到branch

```bash
git rebase master

or

git merge master

```