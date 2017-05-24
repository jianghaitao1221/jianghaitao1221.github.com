---
layout:     post
title:      "saltstack之salt-ssh安装salt-minion "
subtitle:   "\"saltstack学习十一\""
date:       2017-05-24 04:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## 起因

每开一个新机器，都需要手动安装`salt-minion`,很麻烦。
可以使用`salt-ssh`进行安装。

## salt-ssh

salt在版本0.17.0中引入了`salt-ssh`，它支持通过`ssh`通道来实现通信。通过这种方式，我们可以直接通过`ssh`通道在远程主机上执行使用`saltstack`
，而不需要在远程主机上运行`minion` ，同时又能支持`saltstack`的大部分功能.

当然，由于无客户端本身的局限性，并不能完全取代标准的`saltstack`通信方式。

### 安装

参考[saltstack安装](http://jianghaitao1221.github.io/2016/09/08/saltstack-ubuntu-1th/)

### 配置文件 

配置文件位于`/etc/salt/roster`

```bash
<SaltID>： 	# 目标 ID
  host： 	# 远程主机的 IP 地址或者主机名
  user： 	# 可以登录的用户
  passwd： 	# 可以登录用户的密码（这个也是可选的，可以是priv，指定私钥，也可以在命令行以参数的形式传入）
             # sudo salt-ssh -i 'node1' --priv xxx.pem test.ping
# 可选参数
  port： 	# SSH 端口
  sudo： 	# 是否运行 sudo，设置 True 或者 False
  sudo_user:   # 可以指定root以外的root用户
  tty:         # Boolean: Set this option to True if sudo is also set to
               # True and requiretty is also set on the target system
               # 远程执行命令时，SSH默认不会分配tty。没有tty，sudo就无法在获取密码时关闭回显。
               # 以下与本参数无关
                    # 使用-tt选项强制SSH分配tty（使用两次-tt）。
                    # 另一方面，sudoers中的Defaults选项requiretty要求只有拥有tty的用户才能使用sudo 设置Defaults    requiretty。

  priv： 	# SSH 私钥的路径，默认是 salt-ssh.rsa
  timeout：  # 连接 SSH 时的超时时间
  minion_opts: # Dictionary of minion opts
  thin_dir：   # 目标系统salt的存储路径，默认是 /tmp/salt-<hash> ,这是一个临时的，如果你想永久存储，需要修改它
  cmd_umask:   # salt-call命令的权限. 
               # Should be in octal (so for 0o077 in YAML you would do 0077, or 63)
```

[官方文档](https://docs.saltstack.com/en/latest/topics/ssh/roster.html#ssh-roster)

### 配置文件示例

```bash
sudo vi /etc/salt/roster

node1:
  host: 192.168.11.11
  port: 22
  user: root

node2:
  host: 192.168.11.12
  port: 22
  user: root
```

### 执行连接测试命令

```bash
sudo salt-ssh -i 'node1' --priv xxx.pem test.ping
```

### 配置sls

```bash
salt-minion-repo:
  pkgrepo.managed:
    - humanname: SaltStack Repo
    - name: deb http://repo.saltstack.com/apt/ubuntu/16.04/amd64/latest {{ grains['lsb_distrib_codename'] }} main
    - dist: {{ grains['lsb_distrib_codename'] }}
    - key_url: https://repo.saltstack.com/apt/ubuntu/16.04/amd64/latest/SALTSTACK-GPG-KEY.pub
  pkg.latest:
    - name: salt-minion
    - refresh: True

/etc/salt/minion:
  file.managed:
    - source: salt://\{\{ sls \}\}/etc/salt/minion
    - user: root
    - group: root
    - makedirs: True
    - require:
      - pkgrepo: salt-minion-repo

salt-minion:
  service.running:
    - enable: True
    - sig: salt-minion
    - require:
      - file: /etc/salt/minion
    - watch:
      - file: /etc/salt/minion
``` 

#### /etc/salt/minion

找一个已存在`/etc/salt/minion`修改如下：

```bash
master: 你的master ip
```

### 执行sls

```bash
sudo salt-ssh -i 'node1' --priv xxx.pem state.sls minion
```

### 查看key

```bash
sudo salt-key -L
```

## 官方文档

[salt-ssh官方文档](https://docs.saltstack.com/en/latest/topics/ssh/)
