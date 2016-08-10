---
layout:     post
title:      "ubuntu下wps不能输入中文"
subtitle:   " \"环境变量未正确设置\""
date:       2016-08-10 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
---

## 输入法

搜狗

## excel

解决`word`不能输入中文

```
sudo vi /usr/bin/wps
#在文件头部添加
#!/bin/bash
export XMODIFIERS="@im=fcitx"
export QT_IM_MODULE="fcitx"

```

## 表格

解决表格不能输入文字

```
sudo vi /usr/bin/et
#在文件头部添加
#!/bin/bash
export XMODIFIERS="@im=fcitx"
export QT_IM_MODULE="fcitx"
```

## ppt

```
sudo vi /usr/bin/wpp
#在文件头部添加
#!/bin/bash
export XMODIFIERS="@im=fcitx"
export QT_IM_MODULE="fcitx"
```