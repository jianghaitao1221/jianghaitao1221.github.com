---

layout:     post
title:      "saltstack删除死掉的minion的key"
subtitle:   "saltstack学习之十四"
date:       2017-11-02 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## 现象

某些 minion 关闭了，但是在 master 上还存在

```bash
sudo salt-key -L
#结果

Accepted Keys:
test-1
test-2 # 死掉的 minion
Denied Keys:
Unaccepted Keys:
Rejected Keys:
```

## 原因 

是因为我们缓存了 minion 的 key 文件，而 saltstack 推荐我们缓存

## 方法

### 命令

```bash
salt-run manage.down removekeys=True
#自动化
*/5 * * * * salt-run manage.down removekeys=True
```

### Thorium

```bash
#/etc/salt/master
engines:
  - thorium: {}
thorium_roots:
  base:
    - /srv/thorium/base
#/srv/thorium/base/top.sls
base:
  '*':
    - key_clean

#/srv/thorium/base/key_clean.sls
statreg:
  status.reg

keydel:
  key.timeout:
    - delete: 60
    - require:
      - status: startreg
##/etc/salt/minion
beacons:
  status:
    - interval: 10
# 重启 master 和 minion

```

[参考文档](https://docs.saltstack.com/en/latest/topics/thorium/index.html)

我配置没好使，看 debug 日志 当 minion 或者的时候， minion 是发送心跳的， keydel 也在运行， 当 minion dead 了， keydel不运行了

#### salt 版本

2017.7.2




