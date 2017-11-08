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

### 自动化删除

#### reactor system

其实Reactor就是匹配event的tag，如果发现有和我定义相匹配的tag，那么我就可以触发相应的操作。

`event 会在另外的章节讲解`

```bash
#开启presence事件，在master配置文件中设置

#/etc/salt/master.d/presence_events.conf
presence_events: True

#查看事件
sudo salt-run state.event pretty=True

#salt/presence/change事件的样子
salt/presence/change	{
    "_stamp": "2017-11-08T07:54:43.260001", 
    "lost": [
        "test-salt-minion-1"
    ], 
    "new": []
}

#/etc/salt/master.d/reactor.conf
reactor:
  - 'salt/presence/change': #监听的时间标签
    - salt://_reactor/delete-key-stopped-minion.sls

#delete-key-stopped-minion.sls
\{\% for minion_id in data['lost'] \%\}
remove_unused_keys_\{\{ minion_id \}\}:
  wheel.key.delete:
    - args:
      - match: \{\{ minion_id \}\}
\{\% endfor \%\}

```

#### 注意

reactor system 只能保障 presence_events 开启后的 minion ，配合 salt-run manage.down removekeys=True。

## 官方文档

[REACTOR SYSTEM](https://docs.saltstack.com/en/latest/topics/reactor/index.html)


[SALT MASTER EVENTS](https://docs.saltstack.com/en/latest/topics/event/master_events.html)


