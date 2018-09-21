---

layout:     post
title:      "搭建DNS服务(十二)"
subtitle:   "PowerDNS的监控"
date:       2018-8-15 09:00:00
author:     "Jht"
header-img: "img/powerdns-bg.jpg"
catalog: true
tags:
    - dns
    - powerdns
---

# PowerDNS 的性能监控

PowerDNS的性能监控可以分为几种：

- 内置的webserver
- carbon
- Prometheus

## 内置的webserver

PowerDNS的所有产品都内置了webserver：

- PowerDNS authoritative
- PowerDNS recursor
- PowerDNS dnsdist（暂时不做具体介绍）

### PowerDNS authoritative

#### 参数

```bash
#webserver的相关参数
webserver=yes
webserver-address=0.0.0.0
#webserver-password=aaaa  访问网站所需的密码
webserver-port=8081
webserver-allow-from=0.0.0.0/0
#api相关参数
api=yes
api-key=bbbb
```

#### 访问

##### webserver

- http://IP:8081/
- 输入密码和api-key

页面截图：

![avatar](/img/in-post/pdns/pdns-webserver1.jpg)


![avatar](/img/in-post/pdns/pdns-webserver2.jpg)


![avatar](/img/in-post/pdns/pdns-webserver3.jpg)

##### api

```bash
curl -v -H 'X-API-Key: aaaa' http://127.0.0.1:8081/api/v1/servers #查看所有server
# [{
# 	"config_url": "/api/v1/servers/localhost/config{/config_setting}",
# 	"daemon_type": "authoritative",
# 	"id": "localhost",
# 	"type": "Server",
# 	"url": "/api/v1/servers/localhost",
# 	"version": "4.1.4",
# 	"zones_url": "/api/v1/servers/localhost/zones{/zone}"
# }]
curl -v -H 'X-API-Key: aaaa' http://127.0.0.1:8081/api/v1/servers/localhost/statistics #查看指标
```

#### 相关文档

[Built-in Webserver and HTTP API](https://doc.powerdns.com/authoritative/http-api/index.html)

[authoritative performance](https://doc.powerdns.com/authoritative/performance.html)


### PowerDNS recursor

#### 参数

```bash
#webserver的相关参数
webserver=yes
webserver-address=0.0.0.0
#webserver-password=aaaa  访问网站所需的密码
webserver-port=8082
webserver-allow-from=0.0.0.0/0
#api相关参数
api-key=bbbb
```

#### 访问

##### webserver

- http://IP:8082/
- 输入密码和api-key

页面截图：

![avatar](/img/in-post/pdns/recursor-webserver1.jpg)

##### api

```bash
curl -v -H 'X-API-Key: aaaa' http://127.0.0.1:8082/api/v1/servers/ #查看所有server
# [{
# 	"config_url": "/api/v1/servers/localhost/config{/config_setting}",
# 	"daemon_type": "recursor",
# 	"id": "localhost",
# 	"type": "Server",
# 	"url": "/api/v1/servers/localhost",
# 	"version": "4.1.4",
# 	"zones_url": "/api/v1/servers/localhost/zones{/zone}"
# }] 
curl -v -H 'X-API-Key: aaaa' http://127.0.0.1:8082/api/v1/servers/localhost/statistics #查看指标
```

#### 相关文档

[Built-in Webserver and HTTP API](https://doc.powerdns.com/recursor/http-api/index.html)

[recursor metrics](https://doc.powerdns.com/recursor/metrics.html)

## carbon

carbon是graphite中负责后端数据采集。
官方文档推荐`metronome`,当然你也可以用其他的支持carbon协议的软件。

### 安装

```bash
sudo apt install libeigen3-dev autoconf automake libtool libboost-all-dev
git clone https://github.com/ahupowerdns/metronome.git
cd metronome
./bootstrap
./configure
make

mkdir stats
./metronome --stats-directory=./stats
```

### 配置

#### authoritative&recursor

```bash
carbon-server=127.0.0.1:2004   #carbon的ip地址
carbon-interval=60             #发送间隔
carbon-ourname=pdns-1          #当前服务的名字，默认是hostname
```

#### metronome

- metronome/html/local.js修改ip为内网或者外网ip
- 使用nginx配置root为metronome/html/

### 查看

通过浏览器访问。

![avatar](/img/in-post/pdns/metronome1.jpg)

![avatar](/img/in-post/pdns/metronome2.jpg)

![avatar](/img/in-post/pdns/metronome3.jpg)

![avatar](/img/in-post/pdns/metronome4.jpg)

![avatar](/img/in-post/pdns/metronome5.jpg)

![avatar](/img/in-post/pdns/metronome6.jpg)

![avatar](/img/in-post/pdns/metronome7.jpg)


### 相关文档

[Graphite官网](https://graphiteapp.org/)

[使用graphite来监控业务系统](https://www.jianshu.com/p/3d98196c4290)

[PowerDNS “Graphing as a Service](https://blog.powerdns.com/2014/12/11/powerdns-graphing-as-a-service/)

[metronome](https://github.com/ahupowerdns/metronome)

[powerdns metronome](https://metronome.powerdns.com/?server=dnsdist.ar-rec1.main&beginTime=-7200)

## Prometheus

### 安装

#### Prometheus

```bash
# Prometheus
wget https://s3-eu-west-1.amazonaws.com/deb.robustperception.io/41EFC99D.gpg | apt-key add -
sudo apt update
sudo aptnn install prometheus prometheus-node-exporter prometheus-pushgateway prometheus-alertmanager
```

#### powerdns_exporter


`因为有的库被墙了，就没用make编译。`

```bash
#PowerDNS Exporter
mkdir -p go/src/github.com/
export GOPATH=${GOPATH-~/go}
cd go/src/github.com/
git clone https://github.com/viq/powerdns_exporter.git
mkdir -p $GOPATH/src/golang.org/x/
cd  $GOPATH/src/golang.org/x/
git clone https://github.com/golang/crypto.git
git clone https://github.com/golang/sys.git
cd $GOPATH/go/src/github.com/powerdns_exporter
go get -v
go install
go build
## run 
go run powerdns_exporter -api-url="http://127.0.0.0:8081/api/v1/" -api-key="aaaa"
```

#### 查看

- http://IP:9120/metrics

### powerdns_exporter与Prometheus集成

```bash
#打开文件
sudo vi /etc/prometheus/prometheus.yml
#添加
- job_name: "powerdns"
    static_configs:
      - targets: ["127.0.0.1:9120"]
#重启
sudo service prometheus restart
```

### 查看

#### Prometheus Web

自带的比较简陋。

- http://IP:9090/graph

![avatar](/img/in-post/pdns/prometheus1.jpg)

![avatar](/img/in-post/pdns/prometheus2.jpg)

#### Grafana

Grafana 是一套开源的分析监视平台，支持 Graphite, InfluxDB, OpenTSDB, Prometheus, Elasticsearch, CloudWatch 等数据源，其UI非常漂亮且高度定制化。

```bash
#打开
sudo vi /etc/apt/sources.list.d/grafana.list
#写入
deb https://packagecloud.io/grafana/stable/debian/ stretch main

sudo curl https://packagecloud.io/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana

sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable grafana-server
sudo service grafana-server start
```

##### 访问

- http://52.81.51.124:3000
- 使用默认用户admin/admin进入
- 在 Dashboard 首页，点击添加数据源

  - ![avatar](/img/in-post/pdns/grafana.jpg)
  - ![avatar](/img/in-post/pdns/grafana1.jpg)
- 自定义监视画板
  - 点击侧边栏的 “+” 或者dashboard的manage 选项
  - 选择Graph类型
  - 点击Panel Title -> Edit 进入Panel编辑页面，并在Metrics中的 Metric lookup 选择 
  - ![avatar](/img/in-post/pdns/grafana2.jpg)


### 相关文档

[powerdns_exporter](https://github.com/viq/powerdns_exporter)

[Prometheus 实战](https://legacy.gitbook.com/book/songjiayang/prometheus/details)

[Prometheus官网](https://prometheus.io/)

[grafana官网](https://grafana.com/)

[Installing on Debian / Ubuntu](http://docs.grafana.org/installation/debian/)