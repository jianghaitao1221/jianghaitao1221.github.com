---
layout:     post
title:      "salt-api配置"
subtitle:   "\"saltstack学习三\""
date:       2016-09-12 00:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - ubuntu
    - devops
---



## 安装salt-api

在[saltstack安装](https://jianghaitao1221.github.io/2016/09/08/saltstack-ubuntu-1th/)已安装

## 为salt-api添加用户

`salt-api`使用`eauth`验证系统（使用api所在机器的账户进行验证）

```bash
useradd -M -s /sbin/nologin salt
echo "sudopsw" | sudo -S echo salt:salt_pass | sudo chpasswd
```


## 生成证书

`如果有证书，直接看`[配置](#配置)

### 安装依赖

```bash
sudo apt-get install python3-openssl
```

### 生成 

```bash
salt-call --local tls.create_self_signed_cert
```

## 配置

### 配置eauth

```bash
 vi /etc/salt/master.d/eauth.conf


 #写入
 external_auth:
  pam:
    salt:
      - .*
      - '@runner'
      - '@wheel'
```

### 配置salt-api

```bash
vi /etc/salt/master.d/api.conf


#写入
rest_cherrypy:
  port: 8000
  ssl_crt: /etc/pki/tls/certs/localhost.crt
  ssl_key: /etc/pki/tls/certs/localhost.key
```

## 重启服务

```bash
systemctl restart salt-api.service
systemctl restart salt-master.service
```

## 登陆

```bash
curl -k https://127.0.0.1:8000/login -H "Accept: application/x-yaml" -d username='salt' -d password='salt_pass' -d eauth='pam'


#返回
return:
- eauth: pam
  expire: 1473725478.228444
  perms:
  - .*
  - '@runner'
  - '@wheel'
  start: 1473682278.228443
  token: b896ceb607daaaaaaaaaaaaaaaaaaaa88d61
  user: saltap
```

## 问题

### 现象一

```bash
#执行
curl -k https://127.0.0.1:8000/login -H "Accept: application/x-yaml" -d username='salt' -d password='salt_pass' -d eauth='pam'

#返回

<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"></meta>
    <title>401 Unauthorized</title>
    <style type="text/css">
    #powered_by {
        margin-top: 20px;
        border-top: 2px solid black;
        font-style: italic;
    }

    #traceback {
        color: red;
    }
    </style>
</head>
    <body>
        <h2>401 Unauthorized</h2>
        <p>Could not authenticate using provided credentials</p>
        <pre id="traceback"></pre>
    <div id="powered_by">
    <span>Powered by <a href="http://www.cherrypy.org">CherryPy 3.2.2</a></span>
    </div>
    </body>
</html>
```

### 现象二

偶尔出现现象一

### 解决办法

#### 验证

```bash
sudo salt -a pam '*' test.ping

#输入用户名
salt

#输入密码
salt_pass

## 返回
xxx：
  True #证明设置的eauth是成功的
```

### 解决 

应该是有缓存，不太清楚具体机制，只能暴力解决。以后在分析。

```bash
sudo reboot
```
