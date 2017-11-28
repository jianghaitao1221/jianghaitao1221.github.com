---

layout:     post
title:      "ssh-agent forwarding"
subtitle:   "跳转机配置"
date:       2017-11-06 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - linux
    - ssh
---

## ssh agent


ssh-agent是一个运行在后台的程序，用于管理本地的keys。

通常启动ssh-agent时，会加载用到的keys到内存。这样，在实际用到key的时候，就不必每次都输入passphrase密码指定key了。

- Local ---(SSH)---> Server1

```bash
#启动ssh-agent
eval `ssh-agent`
#添加key(private key)
ssh-add ~/.ssh/id_rsa
#查看ssh-agent中管理的keys
ssh-add -L
#连接
ssh -vT user@your Server1 ip
```

## ssh agent forwarding

SSH agent forwarding支持SSH免密码连接第三方远程服务器（如从本地ssh到远程服务器，在远程服务器使用同一个私钥，访问另一台机器）。
SSH agent forwarding是基于ssh-agent的。通过SSH agent forwarding，不仅客户端可以利用本地的ssh-agent管理keys连接远程服务器，而且远程服务器还可以利用客户端本地的ssh-agent访问第三方服务器，就如同ssh-agent运行在远程服务器上一样。

- Local ---(SSH)---> Server1 ---(SSH)---> Server2 

- 方法一

```bash
#在Server1上启动ssh-agent
#确认ssh-agent运行中
echo "$SSH_AUTH_SOCK"
#如果没有，则启动ssh-agent
ssh-agent -s
#添加key(private key)
ssh-add ~/.ssh/id_rsa
#forwarding登录Server2
ssh -A user@your Server2 ip
```

- 方法二

```bash
#修改 ~/.ssh/config 設定，加上 ForwardAgent yes，這樣就不需要每次連都加上 -A 參數。例如
Host Server2
  HostName your Server2 ip
  ForwardAgent yes
```

## 不配置ssh agent的方法

 ```bash
Host Server1
    Hostname your Server1 ip
    User user
    IdentityFile ~/.ssh/xxx.pem
    ServerAliveInterval 15

Host Server2
    Hostname your Server2 ip
    User user
    IdentityFile ~/.ssh/xxx.pem
    ProxyCommand ssh user@your Server1 ip -W %h:%p
    ServerAliveInterval 15
#运行
ssh Server2
```

## 使用xshell

### 第一步

![Alt text](img/in-post/ssh-agent/ssh-agent-1.png)

### 第二步

![Alt text](img/in-post/ssh-agent/ssh-agent-2.png)

### 第三步

![Alt text](img/in-post/ssh-agent/ssh-agent-3.png)

## 参考文档

[Github Using SSH agent forwarding](https://developer.github.com/v3/guides/using-ssh-agent-forwarding/)






