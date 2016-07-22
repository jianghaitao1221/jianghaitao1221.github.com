---
layout:     post
title:      "git config"
subtitle:   " \"git的配置文件的介绍\""
date:       2016-07-22 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
---

# git onfig

对git进行配置的命令

## git config的级别

### local

仓库界别，文件位置`仓库\.git\config`

```bat
#列出仓库级的配置文件
git config –-local -l
```

### global

全局级别（用户界别），文件位置`用户\.gitconfig`

```bat
#列出全局级的配置文件
git config –-global -l
```


## system

系统级别，文件位置`git安装目录\mingw32\etc\gitconfig`

```bat
#列出系统级的配置文件
git config –-global -l
```

