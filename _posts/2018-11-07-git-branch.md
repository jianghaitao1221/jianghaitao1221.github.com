---
layout:     post
title:      "git branch"
subtitle:   " \"分支的相关操作\""
date:       2018-11-07 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

## git branch

### 创建

本地已有`fork`出来的代码

```bash
#1.创建分支
git branch test
#2.切换分支
git checkcout test
#3.创建并且换
git checkout -b test
```
### 关联远程分支

使用git在本地新建一个分支后，需要做远程分支关联。如果没有关联，git会在下面的操作中提示你显示的添加关联。

关联目的是在执行git pull, git push操作时就不需要指定对应的远程分支，你只要没有显示指定，git pull的时候，就会提示你。

```bash
1.第一种
git checkout -b dev
git pull origin dev
2.第二种
git branch --set-upstream-to=origin/test  test #origin/test 远程的test
```
### 拉取远程分支并创建本地分支

```
git fetch origin dev:dev
```
### 查看

```bash
#1.查看所有分支
git branch
#=======结果=======
  master
* test
#2.查看所有分支(本地和远程)
git branch -a
#3.查看远程分支
git branch -r
```

### 删除

```bash
#1.删除本地分支
git branch -d test
#2.删除远程分支
git push origin -d test
```

### 提交分支

```bash
git push origin test
```

## git 中一些选项解释
-d  --delete：删除

-D  --delete --force的快捷键

-f  --force：强制

-m  --move：移动或重命名

-M  --move --force的快捷键

-r  --remote：远程

-a  --all：所有