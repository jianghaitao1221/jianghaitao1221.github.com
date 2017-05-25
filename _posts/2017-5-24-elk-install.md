---

layout:     post
title:      "elk安装在ubuntu 16.04"
subtitle:   "elk学习一"
date:       2017-5-24 09:00:00
author:     "Jht"
header-img: "img/elk-bg.png"
catalog: true
tags:
    - elk
    - devops
---

## elk

### Elasticsearch

`ElasticSearch`是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java开发的，并作为Apache许可条款下的开放源码发布，是当前流行的企业级搜索引擎。设计用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。

### Logstash

`Logstash`是一种分布式日志收集框架，开发语言是JRuby，当然是为了与Java平台对接，不过与Ruby语法兼容良好，非常简洁强大。

当然它可以单独出现，作为日志收集软件，你可以收集日志到多种存储系统或临时中转系统，如MySQL，redis，kakfa，HDFS, lucene，solr等并不一定是ElasticSearch。

### Kibana

`Kibana`是一个为 Logstash 和 ElasticSearch 提供的日志分析的 Web 接口。可使用它对日志进行高效的搜索、可视化、分析等各种操作。

## 机器配置

- `Elasticsearch`的最小内存是`1G`，实际会占用你一般的内存
- `Logstash`的最小内存是`1G`

我起了个内存`ram:4GB,cpu:2`的机器，自己跑着玩，纯练手用，也没啥数据，他们3个分别占用8%左右的内存，cpu占用0.3%。

`如果你是自己练手`,以下配置足够用了：

- cpu：2
- ram: 4GB

当然，实际上CPU、内存和存储的数量取决于你打算收集日志的体积。需要根据你的需求定制。

[硬件的官方文档](https://www.elastic.co/guide/en/elasticsearch/guide/current/hardware.html#hardware)

## 安装

### Java

`Elasticsearch和Logstash依赖Java`

参考[ubuntu16.04安装工作环境-安装jdk](http://jianghaitao1221.github.io/2016/05/31/ubuntu-16.04/)

### Elasticsearch

```bash
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb http://packages.elastic.co/elasticsearch/2.x/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-2.x.list
sudo apt-get update
sudo apt-get -y install elasticsearch

sudo systemctl daemon-reload
sudo systemctl enable elasticsearch
sudo systemctl restart elasticsearch

# 配置文件地址 /etc/elasticsearch/elasticsearch.yml
# 默认配置地址 /etc/default/elasticsearch

```

### Kibana

```bash
echo "deb http://packages.elastic.co/kibana/4.5/debian stable main" | sudo tee -a /etc/apt/sources.list

sudo apt-get update
sudo apt-get -y install kibana

sudo systemctl enable kibana
sudo systemctl start kibana

# 默认配置地址 /etc/default/kibana
```

### Nginx

`可选`

```bash
sudo apt-get -y install nginx
sudo vi /etc/nginx/sites-available/default
#输入以下配置
# --------begin----------
server_name 你的机器ip;
location / {
        proxy_pass http://localhost:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;        
}
# ----------end--------
sudo nginx -t
sudo systemctl restart nginx

```

### Logstash

#### 安装

```bash
echo "deb http://packages.elastic.co/logstash/2.3/debian stable main" | sudo tee -a /etc/apt/sources.list
sudo apt-get update
sudo apt-get -y install logstash

# 默认配置地址 /etc/default/logstash
```

#### 配置

##### input

`日志格式是json`、

```bash
cd /etc/logstash/conf.d
vi filebeat-input.conf
#输入以下配置
input {
  beats {
    port => 5044
  }
}
```

##### filter

```bash
cd /etc/logstash/conf.d
vi mylog-filter.conf
#输入以下配置
filter {
    json{
    	source => "message"
     }
 }
```

##### output

```bash
cd /etc/logstash/conf.d
vi mylog-output.conf
#输入以下配置
output {
  elasticsearch {
    hosts => ["localhost:9200"]                 # elasticsearch 地址
    index => "logstash-%{type}-%{+YYYY.MM.dd}"  # 索引名称，kibana用得到
    document_type => "%{type}"
    flush_size => 20000                         # 满多少flush
    idle_flush_time => 1                        # 最多多少秒flush
    sniffing => true
    template_overwrite => true
    manage_template => false
    codec => "json"                             # 到elasticsearch，json格式日志的每个属性，是elasticsearch的一个属性
  }
}
```
##### 启动

```bash
sudo systemctl enable logstash
sudo systemctl start logstash
```

### Filebeat

#### 安装

`Filebeat`安装与要收集日志的机器上，我测试用，直接安装在本机了

```bash
echo "deb https://packages.elastic.co/beats/apt stable main" |  sudo tee -a /etc/apt/sources.list.d/beats.list
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get update
sudo apt-get install filebeat
```

#### 配置


```bash
sudo vi /etc/filebeat/filebeat.yml

#1.设置日志位置，修改paths，paths是指定要监控的日志
#修改前
paths:
    - /var/log/auth.log
    - /var/log/syslog
    # - /var/log/*.log
#修改后
paths:
    - /home/xxx/mylog.log

#2.禁用Elasticsearch output
#修改前
output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["localhost:9200"]
#修改后
#output.elasticsearch:
  # Array of hosts to connect to.
#  hosts: ["localhost:9200"]

#3.启用logstash输出
#修改前
#logstash:
    # The Logstash hosts
    #hosts: ["localhost:5044"]
#修改后
logstash:
    # The Logstash hosts
    hosts: ["localhost:5044"]

```

#### 启动

```bash
sudo systemctl enable filebeat
sudo systemctl restart filebeat
```

### 测试

向`/home/xxx/mylog.log`中添加json字符串

### 查找

```bash
curl -XGET 'http://localhost:9200/logstash-*/_search?*'
#结果
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 10,
        "successful": 10,
        "failed": 0
    },
    "hits": {
        "total": 11,
        "max_score": 1.0,
        "hits": [
            {
                "_index": "logstash-log-2017.05.23",
                "_type": "log",
                "_id": "AVwzLFhgzJv_Jh9Ulx12",
                "_score": 1.0,
                "_source": {
                    "message": "{\"x_id\": 5 }",
                    "@version": "1",
                    "@timestamp": "2017-05-23T02:39:22.309Z",
                    "count": 1,
                    "beat": {
                        "hostname": "xxxx",
                        "name": "xxxx"
                    },
                    "source": "/home/ubuntu/mylog.log",
                    "offset": 845,
                    "type": "log",
                    "input_type": "log",
                    "fields": null,
                    "host": "xxxx",
                    "tags": [
                        "beats_input_codec_plain_applied"
                    ],
                    "x_id": 5
                }
            }
        ]
    }
}
```

### 使用kibana查看

因为配置了`nginx`,访问`http://ip`

#### Configure an index pattern

 - `Kibana访问的Elasticsearch索引`

 ![elk1](http://jianghaitao1221.github.io/img/in-post/elk1/elk1.png)

 - 点击`Discover`

 ### 参考资料

 [参考资料](https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elk-stack-on-ubuntu-16-04)
