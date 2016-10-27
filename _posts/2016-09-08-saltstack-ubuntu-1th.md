---
layout:     post
title:      "saltstack安装"
subtitle:   "\"saltstack学习一\""
date:       2016-09-08 01:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - ubuntu
    - devops
---



## 配置

配置文件位置`/etc/salt`，`master`配置文件`/etc/salt/master`，`minion`配置文件`/etc/salt/minion`。

### 14.04

#### 第一步 import the SaltStack repository key

```bash
wget -O - https://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
```

#### 第二步 add sources 

```bash
echo deb http://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest trusty main | sudo tee /etc/apt/sources.list.d/saltstack.list
```

#### 第三步

```bash
sudo apt-get update
```

#### 第四部

```bash
### 选择性安装
sudo apt-get install salt-api #salt的rest api服务
sudo apt-get install salt-master #salt的服务端
sudo apt-get install salt-cloud  
sudo apt-get install salt-minion  #salt的客户端
sudo apt-get install salt-ssh 
sudo apt-get install salt-syndic
```

### 16.04

#### 变换key和sources

#### 第一步 import the SaltStack repository key

```bash
wget -O - https://repo.saltstack.com/apt/ubuntu/16.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
```

#### 第二步 add sources 

```bash
echo deb http://repo.saltstack.com/apt/ubuntu/16.04/amd64/latest xenial main | sudo tee /etc/apt/sources.list.d/saltstack.list
```

#### 第三步

```bash
sudo apt-get update
```

#### 第四部

```bash
### 选择性安装
sudo apt-get install salt-api #salt的rest api服务
sudo apt-get install salt-master #salt的服务端
sudo apt-get install salt-cloud  
sudo apt-get install salt-minion  #salt的客户端
sudo apt-get install salt-ssh 
sudo apt-get install salt-syndic
```


## 查看版本 

```bash
salt --version
```

## 查看状态

```bash
sudo service salt-master status
```

## 官方文档

[官方文档](http://docs.saltstack.com/topics/installation/index.html#installation)
[中文文档](http://docs.saltstack.cn/topics/installation/index.html#installation) 
