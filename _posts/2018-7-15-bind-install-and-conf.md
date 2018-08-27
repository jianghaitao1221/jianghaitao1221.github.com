---

layout:     post
title:      "搭建DNS服务(三)"
subtitle:   "在ubuntu16.04上bind的搭建与配置"
date:       2018-7-15 09:00:00
author:     "Jht"
header-img: "img/bind-bg.png"
catalog: true
tags:
    - dns
    - bind
---

# bind

1984年，加州大学伯克利分校的几个学生做的，名字叫做Berkeley Internet Name Domain（BIND）。

## 安装

```bash
sudo apt-get install bind9
```

## 配置

配置文件目录`/etc/bind/`

主要配置文件`/etc/bind/named.conf`

```bash
/etc/bind/named.conf.options #存放bind的启动参数
/etc/bind/named.conf.local   #一些注解
/etc/bind/named.conf.default-zones #默认的zone配置
```

### acl-访问控制区域

定义一个访问控制列表

bind内置了4个acl分别是:
- any 对应所有的,也就是0.0.0.0/0.
- none 对应为空.
- localhost 对应本地机器.
- localnets 对应本地网络.

```bsah
acl aa {
            11.11.0.0/8;
            10.10.10.10;
};
```

### options-服务器全局选项

```bash
options
{
        directory "/var/bind/cache"; #默认的路径，没写绝对路径，都是从这个路径开始
        dump-file  "data/cache_dump.db"; #数据库路径，rndc dumpdb命令用到，等于/var/bind/cache/data/cache_dump.db
        statistics-file "data/named_stats.txt"; #服务器统计信息文件的路径，rndc stats使用
        memstatistics-file "data/named_mem_stats.txt"; #每次访问的使用的内存信息
        listen-on port 53 {127.0.0.1;192.168.1.12;} #ip4的监听的地址是在花括号里配置的，需要外网地址就再加一个
        listen-on-v6 port 53 {::1;} #ip6的，同ip4，可以关掉
        allow-query { any; }; #允许访问
        recursion yes; #递归

};
```

###  logging-日志配置

#日志处理方式配置，bind9有很强大的日志处理能力，可以满足不同用户的要求，具体要参考相关资料


```bash
logging
{
        channel my_log {  #定义的my_log通道
                file "data/named.run"  versions 5 size 50m; #日志文件 ，5个版本，大小50m
                #[versions number|unlimited] [size sizespec]; |syslog optional_facility;|null;|stderr; 
                #versions指定允许同时存在多少个版本的日志文件，比如指定3个版本（version 3），就会保存logfile.log、logfile.log0、logfile.log1 和logfile.log2，然后建立一个新的log_file.log进行写入，unlimited表示无限制写入（默认值）
                #size指定文件大小的上限，如果只指定了size而没有指定versions，当文件达到指定的上限时，BIND将停止写入该日志文件。
                severity dynamic; #日志级别，从高到低 critical,error,warning,notice,info,debug [ level ],dynamic
                print-category   yes; #写入日志类别
                print-severity   yes; #写入日志级别
                print-time no;  #不写入日志级别                                                        ④  
        };
        category queries { #category用于指定需要记录的内容。
                my_log;
        };    
        category xfer-out { null; }; #丢弃
};
```

category用于指定需要记录的内容。

- client：处理客户端请求。
- config：配置文件分析和处理。
- database：同BIND内部数据库相关的消息，用来存储区数据和缓存记录。
- default：匹配所有未明确指定通道的类别。
- dnssec：处理DNSSEC签名的响应。
- general：包括所有未明确分类的BIND消息。
- lame-servers：发现错误授权。
- network：网络操作。
- notify：区更新通知消息。
- queries：查询日志。
- resolver：名字解析，包括对来自解析器的递归查询信息。
- security：批准/非批准的请求。
- update：动态更新事件。
- xfer-in：从远程名字服务器到本地名字服务器的区传送。
- xfer-out：从本地名字服务器到远程名字服务器的区传送。

### view-视图

view语句定义了视图功能。视图是BIND 9提供的强大的新功能，允许DNS服务器根据客户端的不同有区别地回答DNS查询，每个视图定义了一个被特定客户端子集见到的DNS名称空间。

**注意**：
- 一旦启用了view，所有的zone都只能定义在view中
- 仅在允许递归请求的客户端所在view中定义根区域
- 客户端请求到达时，是自上而下检查每个view所服务的客户端列表

```bash

view "aa" {
	match-clients { 1.1.0.0/16; 2.2.2.2/28; }; # 允许访问，可以填写多个，可以使acl的值
	recursion yes; # 运行递归查询
	zone "example.com" {
		type master;
		file "example-internal.db";
	};
  include "/etc/named.rfc1912.zones";
};
```


### key and controls

controls定义rndc命令使用的控制通道，若省略，则只允许经过rndc.key认证的127.0.0.1的rndc控制

key语句定义了用于某个特定服务器身份验证的有名字的加密密钥。

```bash
key my_key #key-id
{
        algorithm hmac-md5; #加密算法，TSIG有多种加密算法，但是bind只实现了hmac-md5一种
        secret "vfmD0+avahgW0wa8FQ54EQ=="
};

controls {
        inet 127.0.0.1 port 953 #监听的ip和端口
                allow { 127.0.0.1; } keys { "my_key"; }; #允许访问的列表和使用的key
};
```

### zone

```bash
zone "example.com" {
  type master;
  file "/etc/bind/db.example.com";
};

sudo vi /etc/bind/db.example.com
#内容如下：
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
1              ; Serial  #  每次变更区域内容时数值变化，以通知slave同步数据。
604800         ; Refresh #  更新频率 slave主动向master更新
86400          ; Retry   #  如果 slave 在进行更新失败后，要隔多久再进行重试
2419200        ; Expire  #  是记录逾期时间：当 slave 一直未能成功与 master 取得联系，
                         #  那到这里就放弃 retry，同时这里的资料也将标识为过期
604800 )       ; Negative Cache TTL # TTL 值，如果您在前面没有用“$TTL”来定义，就会以此值为准。 
;
@       IN      NS      localhost.
@       IN      A       127.0.0.1
```

具体解析 ，参见[DNS的记录类型](http://jianghaitao1221.github.io/2018/07/14/dns/#DNS的记录类型)

#### TTL

TTL(Time-To-Live/生存时间)，是一个域名解析记录在DNS服务 中的存留时间。各地的DNS服务器在接受到解析请求后，会向域名指定的NS服务器发出解析请求从而获得解析记录；在获得这个记录之后，记录会在DNS服务器中保存一段时间，在这段时间内若再次接到解析请求，DNS服务器将直接返回刚才获得的记录。

`具体的值参考可以参考同类网站`

```bash
dig +trace +nocmd +noall +answer +ttlid aaaa www.baidu.com #查询ttl值
dig +noauthority +noquestion +nostats www.baidu.com        #查询剩余的ttl值
```

### statistics

```bash
options
{
        ......
        statistics-file "data/named_stats.txt";
        zone-statistics yes;
        ......
};

statistics-channels {
        inet 10.1.10.10 port 8080 allow { 11.111.1.1; };
        inet 127.0.0.1 port 8080 allow { 127.0.0.1; };
};

zone "example.com" {
  type master;
  file "/etc/bind/db.example.com";
  zone-statistics yes;
};

```
#### 查看

##### 命令

```bash
rndc stats
```
##### http

```bash
curl http://bind:8080/xml
curl http://bind:8080/json
curl -j http://bind:8080/json 2>/dev/null | jq '.' | more
curl -j http://bind:8080/json 2>/dev/null | jq '.rcodes'
```

##### 图像化工具

[cacti 官网](https://www.cacti.net/)

## rndc-远程控制

通过这个工具可以在本地或者远程了解当前服务器的运行状况，也可以对服务器进行关闭、重载、刷新缓存、增加删除zone等操作。
可以进行不停服操作。

### 第一步：生成rndc.conf文件

```bash
rndc-confgen -r/dev/urandom >rndc.conf
#文件如下
cat rndc.conf

###############################################
# Start of rndc.conf
key "rndc-key" {
  algorithm hmac-md5;
  secret "Vca5wa2GHCzaU7ju+ajC1Q==";
};

options {
  default-key "rndc-key";
  default-server 127.0.0.1;
  default-port 953;
};
# End of rndc.conf

# Use with the following in named.conf, adjusting the allow list as needed:
# key "rndc-key" {
#   algorithm hmac-md5;
#   secret "Vca5wa2GHCzaU7ju+ajC1Q==";
# };
# 
# controls {
#   inet 127.0.0.1 port 953
#   allow { 127.0.0.1; } keys { "rndc-key"; };
# };
# End of named.conf
###############################################

#可以配置多个server

server aa {
       key "rndc-key";
      addresses   { localhost port 5353; };
};
 
```
### 第二步：将文件End of rndc.conf，后面的加入到named.conf

详见 [key and controls](#key and controls)

移掉rndc.key

重启bind

### 第三步：配置客户端

将rndc.conf发送到客户端

```bash
sudo apt install bind9util
# 文件位置/etc/bind/rndc.conf
```

### 常用命令

```bash
rndc [-b source-address] [-c config-file] [-k key-file] [-s server] [-p port] [-q] [-r] [-V] [-y key_id] {command}
```

**常用命令：**
- status 显示bind服务器的工作状态
- reload 重新加载配置文件和区域文件
- reload zone_name 重新加载指定区域
- reconfig 重读配置文件并加载新增的区域
- querylog 关闭或开启查询日志
- freeze 暂停更新所有动态zone

剩下的command

```bash
man rndc
```

### chroot模式

bind-chroot是bind的一个功能,使bind在chroot的模式下运行。bind运行时的根目录,并不是系统的根目录,只是自定义的一个子目录.这样做提高了安全性。因为在chroot的模式下,bind可以访问的范围仅限于这个子目录的范围里。无法进入到系统的其他目录中。

#### 配置

```bash
#创建目录
mkdir -p /chroot/bind #chroot的跟目录
cd /chroot/bind
mkdir -p dev etc/bind run/named usr/lib var/cache/bind

#复制bind的配置文件和运行文件
cp -R /etc/bind /chroot/bind/etc/ 
cp -R /var/cache/bind /chroot/bind/var/cache/

#更改权限
chown bind:bind /chroot/bind/etc/bind/named*
chown bind:bind /chroot/bind/etc/bind/pri*
chown bind:bind /chroot/bind/run/named
chown bind:bind /chroot/bind/var/cache/bind
chown -R bind:bind /chroot/bind/etc/bind

#设置运行需要的环境依赖
mknod /chroot/bind/dev/null c 1 3
mknod /chroot/bind/dev/random c 1 8
chmod 666 /chroot/bind/dev/{null,random}
cp /etc/localtime /chroot/bind/etc/
mount --bind /usr/lib /chroot/bind/usr/li

#设置apparmor
vi /etc/apparmor.d/usr.sbin.named
/chroot/bind//** rw,
sudo service apparmor reload 

#修改启动参数，两种方式
#设置/lib/systemd/system/bind9.service
ExecStart=/usr/sbin/named -u bind -4 -t /chroot/bind -c /etc/bind/named.conf
#设置/etc/default/bind9
OPTIONS="-u bind -4 -t /chroot/bind -c /etc/bind/named.conf"

sudo service bind9 reload 

```

## 调试

```bash
strace named -g -f -u bind -t /etc/bind
```

## 相关资料

[bind](https://www.isc.org/downloads/bind/)

**BIND9解压目录下的arm管理员手册**

[rndc.conf](http://manpages.ubuntu.com/manpages/xenial/man5/rndc.conf.5.html)