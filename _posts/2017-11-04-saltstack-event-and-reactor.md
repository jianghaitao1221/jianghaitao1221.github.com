---

layout:     post
title:      "saltstack之event & reactor"
subtitle:   "saltstack学习之十六"
date:       2017-11-04 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## Event

由于Salt底层已经构建了一套 Event 系统 , 所有的操作均会产生event。

Event 是 saltstack 里面的对每个事件的一个记录，它相比 job 更加底层，event 能记录更加详细的 saltstack 事件，
比如 minion 服务启动后请求 master 签发证书或者证书校验的过程，都能通过event事件来查看整个过程。

[参考资料](https://docs.saltstack.com/en/latest/topics/event/events.html)

### Event type

- Authentication events
- Start events
- Key events
- Job events
- Runner Events
- Presence Events
- Cloud Events

### 查看事件信息

```bash
#cli
sudo salt-run state.event pretty=True
#salt-api
curl -SsNk https://salt-api.example.com:8000/events?token=05A3

#启动一个 minion 
 
#信息 注意事件格式分为标识事件的tag和事件的详细信息
salt/auth	{                                                    #salt/auth 事件tag
    "_stamp": "2017-11-08T07:52:57.605904",  
    "act": "accept", 
    "id": "test-salt-minion-1",                                  #花括号中间是事件数据
    "pub": "xxxx", 
    "result": true
}
minion/refresh/test-salt-minion-1	{
    "Minion data cache refresh": "test-salt-minion-1", 
    "_stamp": "2017-11-08T07:52:57.700055"
}
minion_start	{
    "_stamp": "2017-11-08T07:52:57.861297", 
    "cmd": "_minion_event", 
    "data": "Minion test-salt-minion-1 started at Wed Nov  8 07:52:57 2017", 
    "id": "test-salt-minion-1", 
    "pretag": null, 
    "tag": "minion_start"
}
salt/minion/test-salt-minion-1/start	{
    "_stamp": "2017-11-08T07:52:57.869570", 
    "cmd": "_minion_event", 
    "data": "Minion test-salt-minion-1 started at Wed Nov  8 07:52:57 2017", 
    "id": "test-salt-minion-1", 
    "pretag": null, 
    "tag": "salt/minion/test-salt-minion-1/start"
}
```

### 事件开关

```bash
开启PRESENCE EVENTS
#/etc/salt/master.d/presence_events.conf
presence_events: True #默认是False
#用Salt presence事件后，Salt Master将会周期性地向事件总线发出presence_events

#开启 STATE EVENTS
#/etc/salt/master
state_events: True # 默认是True
启用Salt状态事件后，当Salt状态运行中的每一个功能完成执行后，Salt minion都会发送一个进度事件
```

### 触发事件

```bash
#To fire a local event from the minion on the command line call the event.fire
salt-call event.fire '{"data": "message to be sent in the event"}' 'tag'
#To fire an event to be sent up to the master from the minion call the event.send 
salt-call event.send 'myco/mytag/success' '{success: True, message: "It works!"}'
```

## Beacons 

Beacons为监控系统活动并触发报警事件提供了一种解决方案。我肯定可以利用它来监听其它事情，如系统登录，硬盘使用和数据库服务。
beacons让你能够监控与salt无关的事情以及触发事件。beacon系统允许minion与各种系统进程挂钩并持续监控进程。当监控的系统进程活动发生时，
minion就发送一个事件到salt事件总线。

[参考资料](https://docs.saltstack.com/en/latest/topics/beacons/index.htmll)

### beacons 可监控的事件

- 文件系统变化
- 系统负载
- 服务运行状态
- shell活动，如用户登录
- 网络和磁盘使用情况

### 启用一个Beacon

```bash
#依赖
sudo salt myminion pkg.install python-inotify

#第一步 创建一个文件
sudo vi /etc/important_file
#输入内容
important_config: True


#第二步 添加/etc/salt/minion.d/beacons.conf
beacons:
  inotify:
    /etc/important_file:
      mask:
        - modify
    disable_during_state_run: True  #当beacon结合salt reactor使用时，可能会由reactor引发对minion上配置文件的重新状态同步操作。而这些操作无疑会继续触发beacon的报警事件，进而产生一个环路。为了避免这类问题，增加这个参数

#第三步 修改文件
echo "this is the second testing" >> importantfile  

#第四步 master 开启监听
sudo salt-run state.event pretty=True

#第五步 结果

salt/beacon/larry/inotify//etc/important_file       {
 "_stamp": "2017-11-08T15:59:37.972753",
 "data": {
     "change": "IN_IGNORED",
     "id": "larry",
     "path": "/etc/important_file"
 },
 "tag": "salt/beacon/larry/inotify//etc/important_file"
}
```

## Reactor 

reactor 系统让你能够对任何事件作出响应。

你不只能够在作业和任务完成时作出响应（Salt Master Events），也能够在服务下线，用户登录，文件被更改和在任何地方发送的自定义事件作出反应（Beacons）。

### 配置

```bash
#/etc/salt/master.d/reactor.conf

reactor:                            # Master config section "reactor"

  - 'salt/minion/*/start':          # Match tag "salt/minion/*/start"
    - /srv/reactor/start.sls        # Things to do when a minion starts
    - /srv/reactor/monitor.sls      # Other things to do

  - 'salt/cloud/*/destroyed':       # Globs can be used to match tags
    - /srv/reactor/destroy/*.sls    # Globs can be used to match file names

  - 'myco/custom/event/tag':        # React to custom event tags
    - salt://reactor/mycustom.sls   # Reactor files can come from the salt fileserver
```

#### Salt Master Events Example

[详见saltstack学习之十四](http://jianghaitao1221.github.io/2017/11/02/saltstack-delete-dead-minion/#自动化删除)

#### Beacons Example

接上文的[启用一个Beacon](#启用一个Beacon)

```bash
#/etc/salt/master.d/reactor.conf
reactor:
  - salt/beacon/*/inotify//etc/important_file:
    - /srv/reactor/revert.sls

#/srv/reactor/revert.sls
revert-file:
  local.state.apply:
    - tgt: \{\{ data['data']['id'] \}\}
    - arg:
      - maintain_important_file
    - kwarg:
      - saltenv: central

#/srv/salt/maintain_important_file.sls
important_file:
  file.managed:
    - name: /etc/important_file
    - contents: |
        important_config: True
```

##### tgt_type

```bash
#例1
revert-file:
  local.state.apply:
    - tgt: group1
    - tgt_type: nodegroup
    - arg:
      - maintain_important_file
    - kwarg:
      - saltenv: central
#例2
revert-file:
  local.state.apply:
    - tgt: 'os:ubuntu'
    - tgt_type: grain
    - arg:
      - maintain_important_file
    - kwarg:
      - saltenv: central
```

[TARGETING MINIONS](https://docs.saltstack.com/en/latest/topics/targeting/index.html#targeting)


### 三种REACTION类型

#### Remote execution

使用salt命令在目标minion上执行一些操作，包括应用state或highstate状态）

```bash
<section id>:
  local.<function>:   #function: pkg.install,cmd.run,state.apply等
    - tgt: <target>   #target：minion id，grains，Jinja语法（{{ data['data']['id'] }}）等
    - arg:
        <arguments>   #对应着function
```

#### Runners
使用salt-run命令执行的一些任务

```bash
deploy_app:
  runner.state.orchestrate:
    - args:
      - mods: orchestrate.deploy_app
      - pillar:
          event_tag: \{\{ tag \}\}
          event_data: \{\{ data['data']|json \}\}
#对应的命令
salt-run state.orchestrate mods=orchestrate.deploy_app pillar='{"event_tag": "foo", "event_data": {"bar": "baz"}}'
```

#### Wheel

用于管理Salt环境变量、密钥管理或更新配置，例如删除死掉 minion

[详见saltstack学习之十四](http://jianghaitao1221.github.io/2017/11/02/saltstack-delete-dead-minion/#自动化删除)

#### Caller

Masterless Minions use this Reactor

```bash
touch_file:
  caller.file.touch:
    - args:
      - name: /tmp/foo
#对应命令
salt-call file.touch name=/tmp/foo
```


## 官方文档

[官方文档](https://docs.saltstack.com/en/latest/topics/event/index.html)




