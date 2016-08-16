---
layout:     post
title:      "svn命令行add和delete"
subtitle:   " \"设置所有要被设置的文件\""
date:       2016-08-16 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - svn
---

## add

`windows`和`linux`通用

```
svn add * --force
```

## delete

### windows

```
svn status | findstr /R "^!" > missing.list
for /F "tokens=* delims=! " %%A in (missing.list) do (svn delete "%%A")
del missing.list 2>NUL
```

###linux

```
svn st | grep ^! | awk '{print " --force "$2}' | xargs svn rm
```

