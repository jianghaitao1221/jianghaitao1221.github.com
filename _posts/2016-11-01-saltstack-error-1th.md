---
layout:     post
title:      "saltstack错误:Minion received a SIGTERM. Exiting"
subtitle:   "\"Some salt keys both in accepted keys and denied keys.\""
date:       2016-10-27 04:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



##  现象

### Minion端

`Minion`关机重启。日志里显示:

```bash
#运行
tail -f /var/log/salt/minion

#结果
[salt.utils.parsers][WARNING ][19132] Minion received a SIGTERM. Exiting.
```

### Master端

```bash
#运行
sudo salt-key -L

#结果
Accepted Keys:
test_pc
Denied Keys:
test_pc
Unaccepted Keys:
Rejected Keys:
```

## 原因

`The primary time a key is "denied" is if a minion connects, and tries to authenticate with a public key other than the one the master already has accepted/cached.
 So minions with duplicate IDs would cause this, as well as minions which were rebuilt or had new keys generated, but the key was not deleted on the master.`

## 解决 

删除`Denied Keys`

```bash
salt-key --list=denied -d test_pc
```

或者

去`/etc/salt/pki/master/minions_denied`删除`test_pc`。

重启`Minion`




