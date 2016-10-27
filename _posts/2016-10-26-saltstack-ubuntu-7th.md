---
layout:     post
title:      "saltstack远程命令和top.sls介绍"
subtitle:   "\"saltstack学习七\""
date:       2016-10-26 04:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



## top.sls

`top.sls`是配置管理的入口文件，默认存放在/srv/salt/下. top.sls文件`非必须`。

在大规模的配置管理工作中,我们需要编写大量的`states.sls`文件。`top.sls`是`states`系统的入口文
件,它在大规模配置管理工作中负责指定哪些设备调用哪些`states.sls`文件。

## salt远程命令的语法

```bash
 salt '<Target>' <function> [参数]
```

执行`salt '*' state.highstate` ，master会对Target发出指令，目标机器会对top.sls进行分析，
根据top.sls内匹配规则进行解析,执行，然后结果反馈给 master.

## Target匹配

salt的Target选择很多，支持minion ID，pillar，grains，group，CIDR,compound等.

### Target匹配示例


#### 1.minion ID匹配


##### 1.1通配符

```bash
# 命令行
salt '*' test.ping
#=====================
#在top文件中使用时
base:
  '*':
    - webserver
```

##### 1.2指定ID

```bash
# 命令行
salt 'dbserver' test.ping
#=====================
#在top文件中使用时
base:
  'dbserver'                  
    - redis
```

##### 1.3正则表达式

```bash
# 通过`-E`可以指定使用pcre。如：
salt -E 'dbserver-(prod|dev)' test.ping
#===================================================
#在top文件中使用时，必须通过`match: pcre`来指定，例如:
base:
  'dbserver-(prod|dev)':
    - match:pcre
    - redis

```

##### 1.4列表

```bash
# 命令行
salt -L 'dbserver1,dbserver2,dbserver3' test.ping

#===================================================
#在top文件中使用时，必须通过`match: list`来指定，例如:
base:
  'dbserver1,dbserver2,dbserver3':
    - match:list
    - redis

```

#### 2.Grians匹配

```bash
#命令行中通过`-G`来表明，如：
salt -G 'os:ubuntu' test.ping
#===================================================
#在top文件中使用时，必须通过`match: grain`来指定，例如:
base:
  'os:ubuntu':
    - match: grain
    - webserver
```

#### 3.组匹配

```bash

#命令行中通过`-N`来表明，如：
salt -N group1 test.ping
#===================================================
#在top文件中使用时，必须通过`match: nodegroup`来指定，例如:
base:
  group1:               #组名为group的，组在/etc/salt/master中定义
    - match: nodegroup    
    - dbserver
```

参考[saltstack之group](https://jianghaitao1221.github.io/2016/10/26/saltstack-ubuntu-6th/#示例)


#### 4.Pillar匹配

```bash
# 命令行
salt -I 'role:DB' test.ping
#===================================================
#在top文件中使用时，必须通过`match: pillar`来指定，例如:
base:
  'role:DB':
    - match: pillar
    - redis
```

#### 5.CIDR匹配

```bash
# 命令行
salt -S '192.168.1.0/24' test.ping
#===================================================
#在top文件中使用时，必须通过`match: ipcidr`来指定，例如:
base:
  '192.168.1.0/24': 
    - match: ipcidr
    - webserver
```

#### 6.compound匹配

```bash
# 命令行
salt -C 'webserv* and G@os:Debin or E@web-dec-serv.*' test.ping
#===================================================
#在top文件中使用时，必须通过`match: compound`来指定，例如:
base:
  'webserv* and G@os:Debian or E@web-dc1-serv.*':
  - match: compound
  - webserver
```

参考[saltstack之group](https://jianghaitao1221.github.io/2016/10/26/saltstack-ubuntu-6th/#混合匹配)


### top.sls多环境配置

在`/etc/salt/master`中有例子

```bash
file_roots:                               
  base:                               #没指定环境时则从base目录获取文件
    - /srv/salt/
  dev: 
    - /srv/salt/dev/services          #每一个环境都可以定义多个目录, 优先级关系由定义目录的顺序决定.开发环境有两个目录，
    - /srv/salt/dev/states            #它会从services目录先找，找不到就去states目录找，如果services找到了就不往下找了
  prod:
    - /srv/salt/prod/services
    - /srv/salt/prod/states

```
