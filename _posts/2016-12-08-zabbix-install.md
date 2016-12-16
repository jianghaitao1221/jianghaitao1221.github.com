---
layout:     post
title:      "ubuntu16.04安装zabbix3.0 LTS "
subtitle:   "\"监控工具的学习（server and agent）\""
date:       2016-12-08 04:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - zabbix
    - devops
---



## 简介 

`来源于百度百科`

zabbix（音同 zæbix）是一个基于WEB界面的提供分布式系统监视以及网络监视功能的企业级的开源解决方案。
zabbix能监视各种网络参数，保证服务器系统的安全运营；并提供灵活的通知机制以让系统管理员快速定位/解决存在的各种问题。
zabbix由2部分构成，zabbix server与可选组件zabbix agent。
zabbix server可以通过SNMP，zabbix agent，ping，端口监视等方法提供对远程服务器/网络状态的监视，数据收集等功能，它可以运行在Linux，Solaris，HP-UX，AIX，Free BSD，Open BSD，OS X等平台上。
zabbix agent需要安装在被监视的目标服务器上，它主要完成对硬件信息或与操作系统有关的内存，CPU等信息的收集。

### 特点

- 安装与配置简单，学习成本低 
- 支持多语言（包括中文）
- 免费开源
- 自动发现服务器与网络设备
- 分布式监视以及WEB集中管理功能
- 可以无agent监视
- 用户安全认证和柔软的授权方式
- 通过WEB界面设置或查看监视结果
- email等通知功能等

### 功能

- CPU负荷
- 内存使用
- 磁盘使用
- 网络状况
- 端口监视
- 日志监视

## 安装 


### server

#### 下载zabbix安装包

##### 14.04

```bash
wget http://repo.zabbix.com/zabbix/3.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.0-1+trusty_all.deb
sudo dpkg -i zabbix-release_3.0-1+trusty_all.deb
sudo apt-get update
```

##### 16.04

```bash
wget http://repo.zabbix.com/zabbix/3.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.0-1+xenial_all.deb
sudo dokg -i zabbix-release_3.0-1+xenial_all.deb
sudo apt-get upadate
```

### 安装服务端

```bash
sudo apt-get install zabbix-server-mysql  zabbix-frontend-php
#安装过程中会让你输入mysql root的密码 
```

#### 修改zabbix配置文件

```bash
vi /etc/zabbix/zabbix_server.conf
#前三个是默认的，增加密码
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=your_chosen_password_here
```

#### 配置数据库

```bash
# 第一步 登录
mysql -u root -p
# 第二步 创建用户详见/etc/zabbix/zabbix_server.conf
create user 'zabbix'@'localhost' identified by 'your_chosen_password_here';
create database zabbix;
grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';
flush privileges;
exit;
#第三步 导入库
cd /usr/share/doc/zabbix-server-mysql
sudo gunzip *.gz
cat create.sql | mysql -uroot zabbix -p # 输入root的密码
#第四步 重启zabbix-server服务
sudo service zabbix-server start
```

#### 配置php

```bash
sudo vi /etc/apache2/conf-enabled/zabbix.conf

php_value max_execution_time 300
php_value memory_limit 128M
php_value post_max_size 16M
php_value upload_max_filesize 2M
php_value max_input_time 300
php_value always_populate_raw_post_data -1
#设置时区
php_value date.timezone  Asia/Shanghai
#重启apache2
sudo service apache2 restart
```

#### 配置zabbix

- 地址：http://ip/zabbix
- 用户名/密码：Admin/zabbix

### 安装客户端

```bash
sudo apt-get install zabbix-agent
#修改服务zabbix server ip
sudo vi /etc/zabbix/zabbix_agentd.conf
Server=zabbix server ip
sudo service zabbix-agent restart

```
