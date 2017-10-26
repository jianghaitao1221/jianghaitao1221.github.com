---

layout:     post
title:      "saltstack之pillar"
subtitle:   ""saltstack学习十三\""
date:       2017-10-25 11:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
---

## Pillar是什么？

Pillar是Salt非常重要的一个组件，它用于给特定的minion定义任何你需要的数据，这些数据可以被Salt的其他组件使用。
Salt在0.9.8版本中引入了Pillar。Pillar在解析完成后，是一个嵌套的dict结构；最上层的key是minion ID，其value是该minion所拥有的Pillar数据；
每一个value也都是key/value。

Pillar作为定义minion全局数据的接口. 默认存储在master端, Minion启动时会连接master获取最新的pillar数据. Pillar使用类似于State Tree的结构, 
默认使用 YAML 作为其描述格式。

## Pillar可以用在哪些地方？

- 敏感数据<br/>例如ssh key，加密证书等，由于Pillar使用独立的加密session，可以确保这些敏感数据不被其他minion看到。
- 变量<br/>可以在Pillar中处理平台差异性，比如针对不同的操作系统设置软件包的名字，然后在State中引用。
- 其他任何数据<br/>可以在Pillar中添加任何需要用到的数据。比如定义用户和UID的对应关系，mnion的角色等。
- 用在Targetting中<br/>Pillar可以用来选择minion，使用-I选项。

```bash
nodegroups:
  group1: 'L@foo.domain.com,bar.domain.com,baz.domain.com or bl*.domain.com'
  group2: 'G@os:Debian and foo.domain.com'
  
```

## 怎样定义Pillar数据？

### master配置文件中定义

master配置文件中的所有数据都添加到Pillar中,修改master配置文件，默认是False

```bash
pillar_opts: True
```

### 使用SLS文件定义Pillar

修改master配置文件

```bash
pillar_roots:
  base:
    - /srv/pillar
#这段代码定义了base环境下的Pillar文件保存在/srv/pillar/目录下。与State相似，Pillar也有top file，也使用相同的匹配方式将数据应用到minion上。示例如下：
```

### top.sls

与State相似，Pillar也有top file，也使用相同的匹配方式将数据应用到minion上。示例如下：

```bash
#/srv/pillar/top.sls:
base:
  '*':
    - data
    - packages
#/srv/pillar/packages.sls: 
{% if grains['os'] == 'RedHat' %}
apache: httpd
git: git
{% elif grains['os'] == 'Debian' %}
apache: apache2
git: git-core
{% endif %}
#/srv/pillar/data/init.sls:
role: DB_master
#这段代码表示，base环境中所有的minion都具有packages和data中定义的数据。Pillar采用与file server相同的文件映射方式，在本例中，packages映射到文件/srv/pillar/packages.sls，data映射到/srv/pillar/data/init.sls。注意key与value要用冒号加空格分隔，没有空格的话将解析失败。

#Pillar还可以使用其他的匹配方式来选择minion，下面的例子中，servers只应用到操作系统是Debain的机器：
dev:
  'os:Debian':
    - match: grain
    - servers

```


## Pillar使用

### 查看

```bash
sudo salt '*' pillar.data
```

### 刷新

```bash
#如果定义好的pillar不生效，建议刷新一下试试。
sudo salt '*' saltutil.refresh_pillar
```

### 使用

```bash
#Python API
pillar['flow']['maxconn']
#在state file文件中
{{ salt['pillar.get']('version') }}
#命令行
salt -I 'role:DB*' test.ping
#在 pillar file文件中
```

### 示例

在 github 中搜索 saltstack pillar

#### 获取minion的ip

```bash
 #编辑pillar 
 vi /srv/pillar/ip.sls
 #文件内容
 ip: {{ grains['ipv4'][1] }}
 #执行命令 
 salt '*' pillar.item ip
 
 # 结果
 host2:
     ----------
     ip:
         192.168.18.213
 host1:
     ----------
     ip:
         192.168.18.212

```
#### 为不同的系统设置不同的参数

```bash
{% if grains['os'] == 'RedHat' %}
apache: httpd
git: git
{% elif grains['os'] == 'Debian' %}
apache: apache2
git: git-core
{% endif %}
```

### 资料

[官方文档](https://docs.saltstack.com/en/latest/ref/pillar/index.html)

[参考文档](http://www.ituring.com.cn/article/42398)



