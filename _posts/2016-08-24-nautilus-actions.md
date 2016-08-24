---
layout:     post
title:      "ubuntu自定义鼠标右键菜单"
subtitle:   " \"在终端打开的几种方式\""
date:       2016-08-24 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
---

## 折腾这个的原因

系统自带的终端功能太少，我下了`konsole`，但是鼠标右键菜单中，没有`在konsle中打开`。

## 解决办法

### 在终端打开

```bash
sudo apt-get install nautilus-open-terminal #重启
```

### 自定义菜单


```bash
sudo apt-get install nautilus-actions #重启
```
#### 第一种方法

搜索`nautilus-actions-config-tool` 或者在命令行输入`nautilus-actions-config-tool`
进行编辑

#### 第二种方法
或者在`~/.local/share/nautilus/scripts`文件夹中编写脚本。

例如：

```bash
vi open-in-konsole.sh 

#输入

#!/bin/bash
cd $NAUTILUS_SCRIPT_CURRENT_URI

exec konsole --new-tab --workdir $(pwd)

#保存
:wq

#重启，右键就有了
```





