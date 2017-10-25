---

layout:     post
title:      "saltstack之job managemen"
subtitle:   "saltstack学习十二"
date:       2017-7-19 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## job id

在salt 中，每次执行一次salt命令就会产生一个Job，每个job有一个唯一id

### 格式

- 20170727175613648515
- 格式为%Y%m%d%H%M%S%f

### job文件缓存位置

#### master

- /var/cache/salt/master/jobs

##### 保存时间

/etc/salt/master 中的 keep_jobs ，默认 keep_jobs=24

#### minion

- /var/cache/salt/minion/proc

##### 保存时间

指令执行完毕将结果传送给master后，删除该job文件

## saltutil

```bash
saltutil.running                    #查看minion当前正在运⾏的jobs
                                    #salt '*' saltutil.running
                 
saltutil.find_job <jid>             #查看指定jid的job(minion正在运⾏的jobs)
                                    #salt '*' saltutil.find_job <jid>

saltutil.signal_job <jid> <single>  #给指定的jid进程发送信号
                                    # salt '*' saltutil.signal_job <jid> <single>

saltutil.term_job <jid>             #终⽌指定的jid进程(信号为15)
                                    #salt '*' saltutil.term_job <jid>

saltutil.kill_job <jid>             #终⽌指定的jid进程(信号为9)
                                    #salt '*' saltutil.kill_job <jid>
```

## job runner

```bash
salt-run jobs.active                #查看所有minion当前正在运⾏的jobs(在所有minions上运⾏saltutil.running)
                                    #salt-run jobs.active

salt-run jobs.lookup_jid  <jid>     #从master jobs cache中查询指定jid的运⾏结果
                                    #salt-run jobs.lookup_jid <jid>

salt-run jobs.list_jobs             #列出当前master jobs cache中的所有job
                                    #salt-run jobs.list_jobs
```

## 官方文档

[JOB MANAGEMENT](https://docs.saltstack.com/en/latest/topics/jobs/)