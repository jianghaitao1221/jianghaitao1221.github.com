---
layout:     post
title:      "saltstack之group"
subtitle:   "\"saltstack学习六\""
date:       2016-10-26 03:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



## nodegroups

在实际使用过程中，根据业务需求，需对 minion 进行分组。

### 配置

- 在 master 配置文件 /etc/salt/master中 或 
- 在 master 配置文件增加一行 default_include: master.d/*.conf 在/etc/salt/master/master.d 中配置

```bash
nodegroups:
  group1: 'L@foo.domain.com,bar.domain.com,baz.domain.com or bl*.domain.com'
  group2: 'G@os:Debian and foo.domain.com'
  
```

### 指定nodegroup

#### 命令行

```bash
sudo salt -N group1 test.ping
```

#### top.sls

```bash
base:
  group1:
    - match: nodegroup
    - webserver
```


### 混合匹配

混合匹配是指在操作时使用多种预定义的匹配方法。默认匹配规则是glob. 在使用混合匹配时，需要先匹配前指定letter， 当前支持的“letter”有：

| Letter | 匹配类型            | 例子                                                      |	Alt Delimiter?|
| ------ |:--------------:     | --------------------------------------------------------:|---------------:|
| G      | Grains 单个匹配     | G@os:Ubuntu                                               |Yes
| E      | Minion id正则匹配   | E@web\d+\.(dev\|qa\|prod)\.loc                           |No
| P      | Grains 正则匹配     | P@os:(RedHat\|Fedora\|CentOS)	                             |Yes
| L      | minions列表         | L@minion1.example.com,minion3.domain.com or bl*.domain.com|No
| I      | Pillar 单个匹配     | I@pdata:foobar                                            |Yes
| J      | Pillar 正则匹配     | J@pdata:^(foo\|bar)$                                       |Yes
| S      | 子网/IP地址匹配      | S@192.168.1.0/24 or S@192.168.1.100                        |No
| R      | Range cluster匹配   | R@%foo.bar                                                |No

匹配可以加入`and,or,not`

#### 命令行

例如，想匹配所有minion中主机名(minion id)以webserv开头并且运行在Debian系统上或者minion的主机名(minion id)匹配正则表达式web-dc1-srv.*:

```bash
salt -C 'webserv* and G@os:Debian or E@web-dc1-srv.*' test.ping
```

#### top.sls

```bash
base:
  'webserv* and G@os:Debian or E@web-dc1-srv.*':
    – match: compound
    – webserver
```

#### Grains匹配

- Grains 单个匹配
- Grains 正则匹配

##### 设置Grains

在 minion 配置文件 /etc/salt/minion 或  /etc/salt/minion/minion.d 中配置

```bash
#minion
grains:
  roles:
    - webserver
    - memcache
  deployment: datacenter4
  cabinet: 13
  cab_u: 14-15

#master

nodegroups:
  group1: 'P@role:(webserver|memcache)' #Grains 正则匹配
  group2: 'G@role:webserver'            #Grains 单个匹配
```

#### minion id匹配

需要设置 minion id，默认是用 hostname

```bash
#master
nodegroups:
  group1: 'L@foo.domain.com,bar.domain.com,baz.domain.com or bl*.domain.com'  #minions列表匹配
  group2: 'E@web\d+\.(dev|qa|prod)\.loc'                                      #Minion id正则匹配

```

##### 设置minion id

最好是在服务启动之前设置好minion id，尽量避免服务启动后再修改minion id

- 修改minion配置文件
- 在机器初始化的时候设置hostname

##### 修改minion id

```bash
#停止salt-minion服务
service salt-minion stop
#删除salt-minion公钥文件
rm /etc/salt/pki/minion/minion.pub
rm /etc/salt/pki/minion/minion.pem
#修改新minion_id
echo NewId > /etc/salt/minion_id
## 或
修改minion配置文件
#master上删除旧的key
salt-key -d oldId
#minion端重新启动salt-minion
service salt-minion start

```

#### Pillar 匹配

需要在 minion 上设置 pillar ， minion 需要缓存 pillar ，在 minion 配置文件配置

```bash
minion_pillar_cache: True

#master
nodegroups:
  group1: 'I@pdata:foobar'                 #Pillar 单个匹配
  group2: 'J@pdata:^(foo|bar)$'            #Pillar 正则匹配
```
#### IP地址匹配

个别的IP，可能需要手动添加

```bash
#master
nodegroups:
  group1: 'S@192.168.1.0/24 or S@192.168.1.100'
```

###




### 资料

[官方文档](https://docs.saltstack.com/en/latest/topics/targeting/nodegroups.html)
[混合匹配](https://docs.saltstack.com/en/latest/topics/targeting/compound.html#targeting-compound)
