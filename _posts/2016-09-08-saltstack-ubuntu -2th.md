---
layout:     post
title:      "saltstack配置"
subtitle:   "\"saltstack学习二\""
date:       2016-09-08 02:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - ubuntu
    - devops
---



## 配置

配置文件位置`/etc/salt`，`master`配置文件`/etc/salt/master`，`minion`配置文件`/etc/salt/minion`。


### master配置

```bash
#自动认证，避免手动运行salt-key来确认证书信息
auto_accept: True

## 重启服务
sudo service salt-master restart
```


### minion配置


```bash
sudo vi /etc/salt/minion
#指定master主机IP
master: 192.168.137.7
#修改被控端主机识别ID,建议使用操作系统名来配置
id: NODE1

#重启服务
service salt-minion restart
```

### 测试

#### 查看所有客户端

```bash
#查看salt-key
sudo salt-key -L
```

结果：

```bash
Accepted Keys:
NODE1
Denied Keys:
Unaccepted Keys:
Rejected Keys:
```

#### ping

```bash
sudo salt '*' test.ping
```

结果：

```bash
test1:
    True
```


## 官方文档

[官方文档](http://docs.saltstack.com/ref/configuration/index.html)
[中文文档](http://docs.saltstack.cn/ref/configuration/index.html) 