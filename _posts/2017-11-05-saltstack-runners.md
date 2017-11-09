---

layout:     post
title:      "saltstack之runners & call"
subtitle:   "saltstack学习之十七"
date:       2017-11-05 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## Runners


runner子系统提供在salt master运行的salt模块。
Runners可用功能为列出作业状态，实时查看事件，管理salt文件服务器，查看salt mine数据，发送wake-on-lan到minions，调用webhooks和发送其它http请求等。

`在master上运行`

[模块列表](https://docs.saltstack.com/en/latest/ref/runners/all/index.html#all-salt-runners)

### 配置

```bash
#/etc/salt/master.d/runners.conf
runner_dirs:[/srv/runner]
#可以在里面写脚本
#假如有个脚本
sudo vi xxx.py
#执行方法
sudo salt-run xxx.yyy
```

### Example

 ```bash
#/srv/salt/git-update.sls 需配置git用户名密码
update conf:
  cmd.run:
    - cwd: /srv/salt
    - name: sudo git pull
    - runas: root
#执行方法
sudo salt-run state.sls git-update
```

### manage

#### status

查看 minion 的状态，看是否存活

```bash
#使用方法 
sudo salt-run manage.status
#结果 输出所有已知的minions的状态, 以up和down分组输出
```

#### key_regen

重新生成环境下的所有key

```bash
#使用方法 
sudo salt-run manage.key_regen
#除非你知道在做什么
```

#### down

查看 down 掉/无法连接的 down

```bash
#使用方法 
sudo salt-run manage.down
#可以通过该方法直接删掉down掉的minion
```

[删掉down掉的minion](http://jianghaitao1221.github.io/2017/11/02/saltstack-delete-dead-minion/#命令)

## Call

`在minion上运行` minion 自己执行可执行模块，不是通过 master 下发的 job

### Example

```bash
#在minion本地测试某个sls
sudo salt-call state.sls test.sls test=True
```


## 官方文档



[官方文档-Runners](https://docs.saltstack.com/en/latest/ref/runners/)

[官方文档-Call](https://docs.saltstack.com/en/latest/ref/cli/salt-call.html)




