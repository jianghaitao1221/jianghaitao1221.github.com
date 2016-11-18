---
layout:     post
title:      "ubuntu16.04安装shadowsocks"
subtitle:   " \"两种安装方式，chrome和终端配置代理\""
date:       2016-05-31 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
    - linux
---

## ubuntu16.04安装shadowsocks

`commond`和`GUI`二选一。

### 安装 shadowsocks by commond

##### 安装

```bash
sudo apt-get -y install python-pip
sudo pip install shadowsocks
```
##### 配置

新建名字为`ss_conf.json`的配置文件，内容如下：

```bash
{
    "server":"ip",
    "port": port,
    "password": "password",
    "method": "method", #可选
    "remarks": "remarks",#可选
    "auth": false
}

```

##### 运行

```bash
nohup sslocal -c ~/ss_conf.json > ~/ss.log 2>&1 &
```

###  安装 shadowsocks by GUI

```bash
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

和`windows`一样 找一份`config.json`添加进去。 

### 浏览器

我这里配置的是`chrome`

#### 下载插件

`SwitchyOmega`插件下载方式

- [下载地址](https://github.com/FelisCatus/SwitchyOmega/releases/)
- 谷歌商店

貌似两种方式都需要翻墙，我是一个也没下下来，最后找人下完传给我的。大家可是是第一种。

下载完成后，浏览器地址打开`chrome://extensions/`，将下载的插件拖拽进去安装。

#### 配置插件 

点击`SwitchyOmega`的`Options`

##### 配置Proxy servers 

点击`New profile`配置`Proxy servers` 

- `Protocol`选`SOCKS5`
- `Server`填写`127.0.0.1`
- `Port`填写`1080`
- 点击`Apply changes`

##### 配置 auto swith

点击`auto swith`

- `Default`选择`Direct`
- `Rule list rules`选择刚刚新建的`profile`
- `Rule List Config`选择`AutoProxy`
- `Rule List URL`填写`https://github.com/gfwlist/gfwlist/blob/master/gfwlist.txt`
- 点击`Download Profile Now`
- 点击`Apply changes`

##### 选择 auto swith

点击浏览器右上角的`SwitchyOmega`选择`auto swith`

### 终端

#### 安装 polipo

```bash
sudo apt-get install polipo

vi  /etc/polipo/config
#添加 
socksParentProxy = "localhost:1080"
socksProxyType = socks5
#保存
sudo service polipo restart
```
#### 配置Bash

在`~/.bashrc`中添加如下`alias`，可以方便快速设置`HTTP Proxy`环境变量：

```bash
alias http-proxy-show='env|grep http'
alias http-proxy-on='export http_proxy=http://127.0.0.1:8123; export https_proxy=http://127.0.0.1:8123; http-proxy-show'
alias http-proxy-off='unset http_proxy; unset https_proxy'
```

开启命令`http-proxy-on`

###参考资料


[地址](https://github.com/FelisCatus/SwitchyOmega/wiki/GFWList)



