---
layout:     post
title:      "saltstack之group"
subtitle:   "\"saltstack学习六\""
date:       2016-10-26 03:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



## NODE GROUPS

在SaltStack系统中也可以给Minion定义组角色,但是需要知道Minion ID信息才能把它
定义到某个组中。groups是我们在master（`/etc/salt/master`）配置文件中定义的组名称。

### 示例


修改完`/etc/salt/master`，需重启`master`服务

```bash
nodegroups:
  group1: 'L@foo.domain.com,bar.domain.com,baz.domain.com or bl*.domain.com'
  group2: 'G@os:Debian and foo.domain.com'
  group3: 'G@os:Debian and N@group1'
  group4:
    - 'G@foo:bar'
    - 'or'
    - 'G@foo:baz'
  group5: 'L@test1，test2'
```

测试

```bash
#运行
salt -N group1 test.ping
#结果
test1:
    True
test2:
    True

```

### 混合匹配

| 字母        | 匹配类型           | 例子  |分隔符 （：） |
| ------------- |:-------------:| -----:|-----:|
|G      |Grains glob（Grains键值对） | G@os:Ubuntu |Yes |
|E      | PCRE Minion ID（Minion ID正则）     |   E@web\d+\.(dev\|qa\|prod)\.loc |No |
|P      |Grains PCRE（Grains正则）      |   E@web\d+\.(dev\|qa\|prod)\.loc |Yes |
|L      | List of minions（Minion ID列表） | L@minion1.example.com,minion3.domain.com or bl*.domain.com |No |
|I      | Pillar glob（Pillar键值对）     |   I@pdata:foobar	 |Yes |
|J      | Pillar PCRE（Pillar正则）      |   J@pdata:^(foo\|bar)$	 |Yes |
|S      | Subnet/IP address（子网/IP） | S@192.168.1.0/24 or S@192.168.1.100	 |No |
|R      | Range cluster（客户端范围）    |   R@%foo.bar	|No |

还可以使用 and, or, 和 not。

例如，想匹配所有minion中主机名(minion id)以webserv开头并且运行在Debian系统上或者minion的主机名(minion id)匹配正则表达式web-dc1-srv.*:
salt -C 'webserv* and G@os:Debian or E@web-dc1-srv.*' test.ping
在top.sls中可以如下使用:
base:
  'webserv* and G@os:Debian or E@web-dc1-srv.*':
    – match: compound
    – webserver