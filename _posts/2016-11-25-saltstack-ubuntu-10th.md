---
layout:     post
title:      "saltstack之masterless"
subtitle:   "\"saltstack学习十\""
date:       2016-11-25 04:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



## Masterless

### Minion

Saltstack除了传统的C/S架构外，其实还有Masterless架构，如果采用Masterless架构，我们就不需要单独安装一台Saltstack Master机器，
只需要在每台机器上安装Minion，然后采用本机只负责对本机的配置管理工作机制服务模式。

### 无Minion

某些情况下：你希望只运行sls脚本，不行也要Minion。也OK。

#### 应用场景 

开发中的standlone环境，比如前端的同事，希望自己本地有一套后端服务。能自动更新，回滚，启动，查看，停止服务。

#### 配置

`我已经把默认的minion中file_client修改为file_client: local`

```bash
set -x
SALT_MINION_CONF_PATH='/etc/salt'
SALT_MINION_CONF='/etc/salt/minion'
FILE_ROOT="file_roots:\n  base:\n    - "
PILLAR_ROOT="pillar_roots:\n  base:\n    - "
PWD=$(dirname $(pwd))

wget -O - https://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest/SALTSTACK-GPG-KEY.pub | sudo apt-key add -
echo deb http://repo.saltstack.com/apt/ubuntu/14.04/amd64/latest trusty main | sudo tee /etc/apt/sources.list.d/saltstack.list
apt-get update
apt-get install -y salt-common

mkdir -p $SALT_MINION_CONF_PATH
cp $PWD/conf/minion $SALT_MINION_CONF_PATH

echo "$FILE_ROOT"$PWD"/salt" >> $SALT_MINION_CONF
echo "$PILLAR_ROOT"$PWD"/pillar" >> $SALT_MINION_CONF
```

## 官方文档

[官方文档](https://docs.saltstack.com/en/latest/topics/tutorials/quickstart.html)
