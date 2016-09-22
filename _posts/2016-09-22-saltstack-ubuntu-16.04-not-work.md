---
layout:     post
title:      "salt-api在16.04报错"
subtitle:   "\"python-cherry3.5.0版本不兼容\""
date:       2016-09-22 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - ubuntu
---

## 现象

### 现象一（openssl）
```bash
#测试
openssl s_client -connect ip:port #localhost:8080
#结果
Error:
139707275663224:error:1408F10B:SSL routines:SSL3_GET_RECORD:wrong version number:s3_pkt.c:362:
```

### 现象二（curl）

```bash
#测试
curl -k https://ip:port/login -H "Accept: application/x-yaml" -d username='name' -d password='password' -d eauth='pam'
#结果
curl: (56) GnuTLS recv error (-15): An unexpected TLS packet was received.
```

### 现象三（chrome）

```bash
#测试
https://ip:port
#结果
ERR_SSL_PROTOCOL_ERROR
```

## salt-api版本

```bash
#运行
salt-api --versions-report 
#结果
Salt Version:
           Salt: 2016.3.3
 
Dependency Versions:
           cffi: Not Installed
       cherrypy: 3.5.0
       dateutil: 2.4.2
          gitdb: 0.6.4
      gitpython: 1.0.1
          ioflo: Not Installed
         Jinja2: 2.8
        libgit2: Not Installed
        libnacl: Not Installed
       M2Crypto: Not Installed
           Mako: 1.0.3
   msgpack-pure: Not Installed
 msgpack-python: 0.4.6
   mysql-python: Not Installed
      pycparser: Not Installed
       pycrypto: 2.6.1
         pygit2: Not Installed
         Python: 2.7.12 (default, Jul  1 2016, 15:12:24)
   python-gnupg: Not Installed
         PyYAML: 3.11
          PyZMQ: 15.2.0
           RAET: Not Installed
          smmap: 0.9.0
        timelib: Not Installed
        Tornado: 4.2.1
            ZMQ: 4.1.4
 
System Versions:
           dist: Ubuntu 16.04 xenial
        machine: x86_64
        release: 4.4.0-38-generic
         system: Linux
        version: Ubuntu 16.04 xenial

```


## 原因

`Salt: 2016.3.3`不兼容`cherrypy: 3.5.0`，把它改成`cherrypy: 3.2.2`

## 解决

```bash
mkdir build
cd build
wget http://archive.ubuntu.com/ubuntu/pool/main/c/cherrypy3/cherrypy3_3.2.2-4ubuntu5.debian.tar.gz
wget http://archive.ubuntu.com/ubuntu/pool/main/c/cherrypy3/cherrypy3_3.2.2.orig.tar.gz
wget http://archive.ubuntu.com/ubuntu/pool/main/c/cherrypy3/cherrypy3_3.2.2-4ubuntu5.dsc
sudo apt-get build-dep python-cherrypy3
#如果sudo apt-get build-dep python-cherrypy3不成功
#运行sudo apt install debhelper help2man python-nose python3-all python3-nose python3-setuptools python-epydoc
dpkg-source -x cherrypy3_3.2.2-4ubuntu5.dsc
cd cherrypy3-3.2.2/
dpkg-buildpackage -rfakeroot -b -uc -us
#上面的命令会生成deb
#python-cherrypy3_3.2.2-4ubuntu5_all.deb
#python3-cherrypy3_3.2.2-4ubuntu5_all.deb
sudo dpkg -i python-cherrypy3_3.2.2-4ubuntu5_all.deb
#查看CherryPy版本
python -c "import cherrypy;print cherrypy.__version__"
#锁定版本
sudo apt-mark hold python-cherrypy3
```