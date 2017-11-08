---

layout:     post
title:      "saltstack之grains"
subtitle:   "saltstack学习之十五"
date:       2017-11-03 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## 什么是grains

grains 是 Saltstack 最重要的组件之一，grains 的作用是收集 minion 的基本信息。这些信息通常都是一些静态类的数据，

包括CPU、内核、操作系统、虚拟化等，在服务器端可以根据这些信息进行灵活定制，管理员可以利用这些信息对不同业务进行个性化定制。

## 查看Grains相关的命令及用法

```bash
sudo salt 'test1' sys.list_functions grains
#结果
test1:
    - grains.append
    - grains.delval
    - grains.fetch
    - grains.filter_by
    - grains.get
    - grains.get_or_set_hash
    - grains.has_value
    - grains.item
    - grains.items
    - grains.ls
    - grains.remove
    - grains.set
    - grains.setval
    - grains.setvals

#查看某一个命令的用法
salt 'test1' sys.doc grains.ls
#结果
grains.ls:

    Return a list of all available grains

    CLI Example:

        salt '*' grains.ls

```

### 获取 minion item信息

```bash
 salt 'test1' grains.ls

test1:
    - SSDs
    - biosreleasedate
    - biosversion
    - cpu_flags
    - cpu_model
    - cpuarch
    - disks
    - dns
    - domain
    ....... #后面还有一堆
```

### 查看所有的itmes项

```bash
 salt 'test1' grains.items

test1:
    ----------
    SSDs:
        - xvda
    biosreleasedate:
        02/16/2017
    biosversion:
        4.2.amazon
    cpu_flags:
        - fpu
        - vme
        - de
        - pse
        - tsc
        - msr
        - pae
        - mce
    ....... #后面还有一堆
```

### 查看某项item值

```bash
 salt 'test1' grains os

test1:
    ----------
    os:
        Ubuntu
```

## 自定义 grains

### minion 配置文件

```bash
# /etc/salt/minion.d/grains.conf
grains:
  roles:
    - webserver
    - memcache

#需重启 minion 服务
# 查看
salt 'test1' grains.item roles
#结果
test1:
    ----------
    roles:
        - webserver
        - memcache
```

### Grains模块定义

```bash
#第一种
salt 'test1' grains.append mygrains 'test'
test1:
    ----------
    mygrains:
        - test
#第二种
salt 'test1' grains.setvals "{'mygrains1':'test1','mygrains2':'test2'}"
test1:
    ----------
    mygrains2:
        test2
    mygrains1:
        test1
# 存储位置
/etc/salt/grains
```

## 优先级

- Core grains.
- Custom grains in /etc/salt/grains.
- Custom grains in /etc/salt/minion.
- Custom grain modules in _grains directory, synced to minions.

## 官方文档

[官方文档](https://docs.saltstack.com/en/latest/topics/grains/)



