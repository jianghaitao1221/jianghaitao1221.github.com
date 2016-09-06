---
layout:     post
title:      "git合并commit"
subtitle:   " \"希望只有一个commit\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

##  合并commit

### 第一步

```bash
$ git rebase -i origin/master
```

### 第二步

执行第一步，会进入编辑窗口如下：

```
# 文件头列出提交记录，按时间正序，
pick 123456 aaaaaaaaa
pick 123457 bbbb
pick 123458 ccc
pick 123459 git ccc

# Rebase 8db7e8b..fa20af3 onto 8db7e8b
#
# Commands:
#  p, pick = use commit #正常选中
#  r, reword = use commit, but edit the commit message #选中，并且修改提交信息
#  e, edit = use commit, but stop for amending #选中，rebase时会暂停，允许你修改这个commit
#  s, squash = use commit, but meld into previous commit #选中，会将当前commit与上一个commit合并
#  f, fixup = like "squash", but discard this commit's log message #与squash相同，但不会保存当前commit的提交信息
#  x, exec = run command (the rest of the line) using shell #执行其他shell命令
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

在你要合并提交前把`pick`改成`squash`或`s`，然后保存(有的时候写`s`不起作用)

```
# 把这四个合并成一个提交，后一个合并到前一个
pick 123456 aaaaaaaaa
squash 123457 bbbb
squash 123458 ccc
squash 123459 git ccc

# Rebase 8db7e8b..fa20af3 onto 8db7e8b
#
# Commands:
#  p, pick = use commit #正常选中
#  r, reword = use commit, but edit the commit message #选中，并且修改提交信息
#  e, edit = use commit, but stop for amending #选中，rebase时会暂停，允许你修改这个commit
#  s, squash = use commit, but meld into previous commit #选中，会将当前commit与上一个commit合并
#  f, fixup = like "squash", but discard this commit's log message #与squash相同，但不会保存当前commit的提交信息
#  x, exec = run command (the rest of the line) using shell #执行其他shell命令
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

### 第三步

第二步保存后，会进入修改提交描述信息的界面，如下：

```
# The first commit's message is:
aaaaaaaaa

# This is the 2nd commit message:
bbbb

# This is the 3rd commit message:
ccc
```
根据你的需求修改，修改后保存。


### 第四步

```bash
git push --force
```