---
layout:     post
title:      "saltstack之web界面--->SaltPad"
subtitle:   "\"saltstack学习八\""
date:       2016-10-27 01:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



## 起因

由于不熟悉`saltstack`的小伙伴，要查询服务的状态，开始查找`saltstack`的WebGUI。

## saltstack的WebGUI

### Halite

Halite是saltstack官方的，现在已经不再更新了。

### SaltPad

页面简洁，功能简单，文档比较全，github上标星的比较多，最后一次更新比较新。`执行简单的任务够用了`。

### saltshaker

页面简洁，功能丰富，依赖较多，例如数据库等。最后一次更新是2016年六月。

### SALTSTACK ENTERPRISE

企业版WebGUI。

## 部署

### 安装

```bash

wget https://github.com/Lothiraldan/saltpad/releases/download/v0.3.1/dist.zip
#可以去查看最新的releases，进行下载

unzip dist.zip

mv dist saltpad

sudo cp -R saltpad /data/www

```

### 配置

#### saltapi配置

文件位置`/etc/salt/master.d/api.conf`

```bash

rest_cherrypy:
  port: 8080
  disable_ssl: True
  websockets: True
  cors_origin: '*'
  static: /data/www/saltpad/static
  static_path: /static
  app: /data/www/saltpad/index.html
  app_path: /saltpad

  external_auth:
  pam:
    saltapi:
      - .*
      - '@runner'
      - '@wheel'

```

#### saltpad配置

文件位置`/data/www/saltpad/settings.json`

```json

{
  "API_URL": "127.0.0.1:8080", #saltapi的地址，内网地址，外网地址，域名
  "SECURE_HTTP": false,        #是否用ssl
  "templates": {
    "ping all": {
      "description": "ping and get all minions",
      "matcher": "glob",
      "target": "*",
      "moduleFunction": "test.ping"
    },
    "ping one": {
      "description": "ping one ,check minion is active",
      "matcher": "glob",
      "moduleFunction": "test.ping"
    }
  },
  "EAUTH": "pam",
  "FLAVOUR": "rest_cherrypy",
  "PATH_PREFIX": "/saltpad"
}
```

#### nginx配置

```bash
upstream saltpad {
    server  localhost:8080;
}

server {
    listen          80      default_server;
    
    location / {
        proxy_pass              http://saltpad;

        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_connect_timeout   150;
        proxy_send_timeout      100;
        proxy_read_timeout      100;
        proxy_buffers           4 32k;
        client_max_body_size    8m;
        client_body_buffer_size 128k;
    }
}   
```

## 官方文档

[官当文档](https://github.com/Lothiraldan/saltpad)

