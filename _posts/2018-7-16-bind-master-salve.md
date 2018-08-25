---

layout:     post
title:      "搭建DNS服务(三)"
subtitle:   "bind的master/salve设置"
date:       2018-7-16 09:00:00
author:     "Jht"
header-img: "img/bind-bg.png"
catalog: true
tags:
    - dns
    - bind
---

# master/slave

配置方式：

- 方法一:传统的zone file
- 方法二:web前端GloboDNS

## zone file

- master 的 zone 中有一条NS记录指向slave
- slave只需要定义区域,从master获取file
- bind程序的版本应该保持一致
- master，slave时间需一致

### 配置

#### master

```bash

zone "example.com" IN {
        type master;                   
        file "zones/example.com.db"; #zone的相关数据
};

zone "4.111.11.in-addr.arpa" IN {
        type master;
        allow-transfer { 11.111.4.200; } #允许slave的ip               
        file "zones/named.11.111.4"; 
};
# allow-transfer也可以全局定义
options {
        allow-transfer { 11.111.4.200; } ;
};
```

#### slave

```bash
zone "example.com" IN {
        type slave;                    
        masters { 11.111.4.100; }       
        file "zones/example.com.db";  
};

zone "4.168.192.in-addr.arpa" IN {
        type slave;                     
        masters { 11.111.4.100; };        
        file "zones/named.11.111.4";  
};
# 可以使用TSIG
zone "4.168.192.in-addr.arpa" IN {
        type slave;                     
        masters { 11.111.4.100 key hunk-tech-key; };        
        file "zones/named.11.111.4";  
};
#或给master配置key，key是怎么来的看下一节
server 11.111.4.100 { keys hunk-tech-key; };
```

#### dnssec-keygen生成key

 ```bsah
 #使用命令生成
 dnssec-keygen [-a algorithm] [-b keysize] [-n nametype] [-3] [-A date/offset] [-C] [-c class] [-D date/offset] [-D sync date/offset] [-E engine] [-f flag] [-G] [-g generator] [-h] [-I date/offset]
                     [-i interval] [-K directory] [-k] [-L ttl] [-P date/offset] [-P sync date/offset] [-p protocol] [-q] [-R date/offset] [-r randomdev] [-S key] [-s strength] [-t type] [-V] [-v level]
                     [-z] {name}
 ```

 `master和slave需配置dnssec-key，配置方法同rndc-key一样`

 参见[rndc配置](http://jianghaitao1221.github.io/2018/07/15/bind-install-and-conf/#rndc-远程控制)

 ## GloboDNS

 GloboDNS的原理就是把zone的数据记录到mysql，在数据库中配置好以后，导出成bind的zone file文件，通过rsync同步到对应的master和slave上，通过rndc进行reload
 也就是说你的所有配置都要通过web的前段进行配置。

 ### 安装

**一定要先阅读：**[GloboDNS setup](https://github.com/globocom/GloboDNS/blob/master/doc/setup.md)

有可能会有更新。注意本文写的时间。我的环境是ununtu 16.04

#### 1.单独机器

不能和master或slave在一起，应单独起一台机器

#### 2.用户

master，slave，GloboDNS使用同一用户，
`这只是举例子用globodns用户，你可以自定义，如果自定义的话，以下的globodns需换成your user`

```bash
#在三台机器上，运行id globodns，结果要一样，要权限一致

id globodns
#结果如下
uid=12386(globodns) gid=12386(globodns) groups=25(named),12386(globodns)
    $ id named
    uid=25(named) gid=25(named) groups=25(named)
    $groups globodns named
    globodns : globodns named
    named : named
```

#### 3.chroot模式

 bind需要运行在chroot模式下

#### 4.SSH keypair

rsync需要使用SSH keypair

#### 5.sudo

```bash
globodns  ALL=(ALL) NOPASSWD: /usr/sbin/named-checkconf
```

#### 6.安装依赖

 ```bash
#安装依赖
sudo apt-get install gcc ruby-dev libxslt-dev libxml2-dev zlib1g-dev ruby libffi-dev libmysqlclient-dev mysql-server mysql-client ruby-bundler
gem install bundler
sudo apt install bind9utils
#下载源码
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git clone https://github.com/jianghaitao1221/GloboDNS.git --depth=1 #必须是git工程，代码里用到了最后的时间
cd globodns
# Gemfile.lock   修改 rake 12.3.0 为 rake 12.3.1 这一步因为版本问题，没问题的可以不修改
$ bundle install --deployment --without=test,development 
 ``` 

#### 7.rsync

##### 7.1 启用rsync

ubuntu 16.04默认已安装rsync，rsync服务默认不是启动的，我们要修改下面的文件。

```bash
sudo vi /etc/default/rsync
#修改 false改true
RSYNC_ENABLE=true   
sudo cp /usr/share/doc/rsync/examples/rsyncd.conf /etc

sudo service rsync start
```

##### 7.2 rsync

```bash
#/etc/ssh/ssh_config or ~/.ssh/config

sudo vi /etc/ssh/ssh_config
#添加
Host your ip #192.168.1.* or *
   StrictHostKeyChecking no
   UserKnownHostsFile=/dev/null

```

#### 8.配置数据库

 ```bash
vi config/database.yml
development:
  adapter:  mysql2
  database: globodns
  host: localhost
  username: your username
  password: your password
  。。。。。。
 ```


#### 9.配置数master&slave

 ```bash
vi config/globodns.yml

development: &devconf
    appname: 'globodns'
    bind:
      master:
        # ipaddr_external: '1.2.3.4' # if use a special ip for master for any slave (optional)
        user:            'globodns'
        host:            'your host' #在/etc/hosts 中配置
        ipaddr:          'your up'
        port:            53
        rndc_port:       953
        chroot_dir:      '/chroot/bind'
        zones_dir:       '/etc/bind'
        named_conf_file: '/etc/bind/named.conf'  # must be inside zones_dir
        named_conf_link: '/etc/named.conf'  # can be anywhere
        export_chroot_dir:    <%= Rails.root.join('tmp', 'named', 'chroot_master') %>
      slaves:
        - user:            'globodns'
          host:            'your host' #在/etc/hosts 中配置，不填就没有slave
          ipaddr:          'your up'
          port:             53
          rndc_port:        953
          chroot_dir:       '/chroot/bind'
          zones_dir:        '/etc/bind'
          named_conf_file:  '/etc/bind/named.conf'  # must be inside zones_dir
          named_conf_link:  '/etc/named.conf'  # can be anywhere
          export_chroot_dir: <%= Rails.root.join('tmp', 'named', 'chroot_slave1') %>
          # masters_external_ip: true # if slave use a different ip for communicate to master, this setting should be set to true


    bind_user:                   'globodns'
    bind_group:                  'globodns'
    additional_dns_servers: ['8.8.8.8', '8.8.4.4'] # additional dns servers ips to check if record is responding correctly


    bind_error_log:         '/var/bind/chroot_master/var/log/globodns.error.log'

    rndc_config_file:       '/etc/bind/rndc.conf' #rndc配置文件的位置
    rndc_key_name:          'rndc-key' #rndc key的名字

 ```

#### 10.master&slave配置rndc

文件位置见上一节指定

参见[rndc配置](http://jianghaitao1221.github.io/2018/07/15/bind-install-and-conf/#rndc-远程控制)

#### 11.初始化数据库

```bsah
#会创建一个管理员用户: admin@example.com/password
rake db:setup
rake db:migrate
rake globodns:chroot:create
```

#### 12.导入master和slave的配置

```bsah
ruby script/importer --remote
```

#### 13.启动

```bsah
bundle exec unicorn_rails
```


 # 相关资料

 [GloboDNS](https://github.com/globocom/GloboDNS)