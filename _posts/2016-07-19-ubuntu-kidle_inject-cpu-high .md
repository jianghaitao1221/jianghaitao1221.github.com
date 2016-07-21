---
layout:     post
title:      "ubuntu的kidle_inject占用CPU过高"
subtitle:   " \"换主板后产生的问题\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
---

##  现象

我的主板坏了，换了个主板，开机就发现干啥都卡，就`top`了下

发现好几个`kidle_inject`进程占用cpu在50%左右（一个核对应一个进程）

这个进程是为了让`CPU`不那么热的....


## 解决方法

```bash
#方法一
sudo rmmod intel_powerclamp(重启机器还在)
#方法二
echo "blacklist intel_powerclamp" | sudo tee /etc/modprobe.d/disable-powerclamp.conf
```
