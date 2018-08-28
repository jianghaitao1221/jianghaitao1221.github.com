---

layout:     post
title:      "搭建DNS服务(八)"
subtitle:   "PowerDNS的master/slave设置和web前端的搭建"
date:       2018-7-24 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---


# PowerDNS支持的模式

- master/slave
- native replication(数据库复制)

## one database

`假设我们的DNS服务器是一个私有DNS，或者不跨国家和区域，只服务与内部。`
`而database又高可用，有高性能，那么两个native模式的DNS服务器连接一个数据库的也是可行的。`

## native replication

官方的描述是默认就支持，不需要额外的设置，native replication模式不会发送和响应更新通知。
只要配置mysql或oracle的主从复制就可以了。

为什么复制数据库就可以？和他的机制有关。详见[powerdns内部机制分析](http://jianghaitao1221.github.io/2018/07/23/pdnf-design/)

## master/slave

`master和slave也需要两个数据库，master和slave分别连接自己的数据库。`

假设master IP为11.111.1.1 ns1.example.local
假设slave  IP为11.111.1.2 ns2.example.local

### 配置host

master和slave的/etc/hosts中配置，在DNS服务为生效时，两台机器互通。

```bash
sudo vi /etc/hosts

11.111.1.1 ns1.example.local
11.111.1.2 ns2.example.local

```

### 安装

详见[powerdns的安装和配置](http://jianghaitao1221.github.io/2018/07/22/pdnf-install-and-conf/)

### 配置

#### master

```bash
sudo vi  /etc/powerdns/pdns.conf
#===================================
allow-axfr-ips=11.111.1.2/32 #允许slave来同步变更
master=yes
daemon=yes
guardian=yes
also-notify=11.111.1.2
slave-cycle-interval=60 # 通知和axfr的间隔
disable-axfr=no
default-soa-name=ns1.example.local

sudo service pdns restart
```

#### slave

```bash
sudo vi  /etc/powerdns/pdns.conf
#===================================
slave=yes
allow-notify-from=11.111.1.1/32
slave-cycle-interval=60 
default-soa-name=ns2.example.local

sudo service pdns restart
```

##### 数据库中配置suprmaster

suprmaster会接到通知后，会自动在slave的数据库中穿件对应master数据中的domain

```bash
#INSERT INTO supermasters VALUES ('<your masters IP address>', '<your slaves hostname>', '');
INSERT INTO supermasters ('11.111.1.1', 'ns2.example.local', account) VALUES ('10.0.0.1', 'ns2.example.org', '');
```

### 怎么测试呢？

这个时候就master/slave就配置OK了，那么怎么测试呢？

两种方法：
- 直接在master的数据库中插入数据
- 用web前端

直接操作数据，不安全，还容易出错，web前端就好很多。下面介绍怎么安装web前端。


## web前端

PowerDNS有很多前端，下面举几个例子：

- PowerDNS-Admin
- Opera DNS UI
- DjangoPowerDNS
- Poweradmin

Poweradmin是官方的，但是页面风格是上个世纪的，很丑。
我就选了PowerDNS-Admin。

更多的前端参见[WebFrontends](https://github.com/PowerDNS/pdns/wiki/WebFrontends)

### PowerDNS-Admin

 PowerDNS-Admin使用HTTP API进行操作。一直在更新，相关文档较为全面。

 #### 安装

 ```bash
#设置数据库
mysql -u root -p
CREATE DATABASE powerdnsadmin;
GRANT ALL PRIVILEGES ON powerdnsadmin.* TO 'pdnsadminuser'@'%' IDENTIFIED BY 'p4ssw0rd';
FLUSH PRIVILEGES;
quit
#Install Python 3 devevelopment package
sudo apt-get install python3-dev
sudo apt-get install -y libmysqlclient-dev libsasl2-dev libldap2-dev libssl-dev libxml2-dev libxslt1-dev libxmlsec1-dev libffi-dev pkg-config
#Install yarn to build asset files
sudo curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
sudo echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list
apt-get update -y
apt-get install -y yarn
#Checkout source code and create virtualenv
git clone https://github.com/ngoduykhanh/PowerDNS-Admin.git powerdns-admin
cd powerdns-admin
virtualenv -p python3 flask
 . ./flask/bin/activate
(flask) [khanh@localhost powerdns-admin] pip install -r requirements.txt
 ```

 #### 配置

 ##### 配置master

 master启动api服务

```bash
sudo vi  /etc/powerdns/pdns.conf
#===================================
api=yes
api-key=yourapisecretkey            # 提供给PowerDNS-Admin用
api-logfile=/var/log/pdns-api.log
webserver=yes
webserver-address=127.0.0.1          # web api监听的地址，根据自己的需求，内网或外网
webserver-allow-from=127.0.0.1/32    # 同上
webserver-port=8081                  # 监听的端口
```

#### 配置PowerDNS-Admin

```bash
(flask) [khanh@localhost powerdns-admin] cp config_template.py config.py
(flask) [khanh@localhost powerdns-admin] vi config.py
#====================================
SECRET_KEY = 'yoursessionencryptkey' 
SQLA_DB_USER = 'yourdbusername'      # pdnsadminuser
SQLA_DB_PASSWORD = 'yourdbpassword'  # p4ssw0rd
SQLA_DB_HOST = 'localhost'
SQLA_DB_NAME = 'yourdbname'          # powerdnsadmin
PDNS_STATS_URL = 'http://localhost:8081/'
PDNS_API_KEY = 'yourapisecretkey' 
PDNS_VERSION = '4.0.0'
RECORDS_ALLOW_EDIT = ['A', 'AAAA', 'CNAME', 'SPF', 'PTR', 'MX', 'TXT', 'SRV', 'NS', 'SOA']

```

##### 创建数据库

```bash
(flask) [khanh@localhost powerdns-admin] export FLASK_APP=app/__init__.py
(flask) [khanh@localhost powerdns-admin] flask db upgrade
```

##### 生成asset files

```bash
(flask) [khanh@localhost powerdns-admin] yarn install --pure-lockfile
(flask) [khanh@localhost powerdns-admin] flask assets build
```

#### 运行和访问

```bash
(flask) [khanh@localhost powerdns-admin] ./run.py
```

访问地址`http://IP:9191`

##### Systemd 

```bash
groupadd powerdnsadmin
useradd --system --user-group powerdnsadmin
sudo vi /etc/systemd/system/powerdns-admin.service
#==================================================

[Unit]
Description=PowerDNS-Admin
After=network.target

[Service]
Type=simple
User=powerdnsadmin
Group=powerdnsadmin
ExecStart=/opt/web/powerdns-admin/flask/bin/python ./run.py
WorkingDirectory=/opt/web/powerdns-admin #你自己的目录 powerdnsadmin要有权限
Restart=always

[Install]
WantedBy=multi-user.target
```

#### PowerDNS-Admin使用

- 新用户可以点击`Create an account`,第一个用户默认为admin

![avatar](/img/in-post/pdns/web1.jpg)

- 页面截屏

![avatar](/img/in-post/pdns/web2.jpg)

##### 设置主从复制的zone

- 点击`Domain Templates`
- 点击`Create Template`
- 输入名字，replication
- 输入名描述 Auto Replication for Slave

添加添加内容如下：

![avatar](/img/in-post/pdns/web3.jpg)

- 创建domain

![avatar](/img/in-post/pdns/web4.jpg)

- 添加Record -->A

![avatar](/img/in-post/pdns/web5.jpg)

- 稍等一小会，让记录飞一会，最晚`slave-cycle-interval`配置的值。

**`注意:`**

`每个自动复制的domain都需要添加replication模板，也就是说都要有那两条NS记录。`

##### 测试

```bash
dig test.example.local A @ns1.example.local
dig test.example.local A @ns2.example.local
```

## 相关资料

[PowerDNS-Admin Wiki](https://github.com/ngoduykhanh/PowerDNS-Admin/wiki)