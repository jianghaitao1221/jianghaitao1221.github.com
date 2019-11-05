---
layout:     post
title:      "数据分析学习（十）"
subtitle:   " \"日志收集\""
date:       2019-10-17 10:00:00
author:     "Jht"
header-img: "img/post-bg-data-analysis.jpg"
catalog: true
tags:
    - analysis
---

## 日志收集工具

### 轻量级

轻量级的Agent有：
- Filebeat
- Fluent-bit

#### 优点	

- 轻量级	
- 零依赖（Fluent-bit编译依赖）	
- 插件多	
  - 多种input	
  - 多种output 	

#### 缺点

轻量级的更多的专注采集，信息转换不是很擅长，一般都是由下游的服务（Logstash，Fluent等）来转换。	

#### Filebeat	

Beats平台集合了多种单一用途数据采集器。	

Beats系列：	

- Filebeat	
- Metricbeat	
- Packetbeat	
- Winlogbeat	
- Auditbeat	
- Heartbeat	
- Functionbeat	

Filebeat是我们最常用的。	

Filebeat是一个开源的文件收集器，也是一款轻量级的日志传输工具。采用go语言开发，重构logstash采集器源码，安装在服务器上作为代理来监视日志目录或特定的日志文件。是elastic家族成员。	

Filebeat可以保持每个文件的状态，并且频繁地把文件状态从注册表里更新到磁盘。这里所说的文件状态是用来记录上一次Harvster读取文件时读取到的位置，以保证能把全部的日志数据都读取出来，然后发送给output。	

如果在某一时刻，作为output的ElasticSearch或者Logstash变成了不可用，Filebeat将会把最后的文件读取位置保存下来，直到output重新可用的时候，快速地恢复文件数据的读取。在Filebaet运行过程中，每个Prospector的状态信息都会保存在内存里。如果Filebeat出行了重启，完成重启之后，会从注册表文件里恢复重启之前的状态信息，让FIlebeat继续从之前已知的位置开始进行数据读取。	
Prospector会为每一个找到的文件保持状态信息。因为文件可以进行重命名或者是更改路径，所以文件名和路径不足以用来识别文件。对于Filebeat来说，都是通过实现存储的唯一标识符来判断文件是否之前已经被采集过。	

如果在你的使用场景中，每天会产生大量的新文件，你将会发现Filebeat的注册表文件会变得非常大。这个时候，你可以参考[the section called “Registry file is too large?edit](https://github.com/elastic/beats/edit/5.1/filebeat/docs/faq.asciidoc)，来解决这个问题。	


#### Fluent bit	

Fluent bit是一个用c写成的插件式、轻量级、多平台开源日志收集工具。它允许从不同的源收集数据并发送到多个目的地。完全兼容docker和kubernetes生态环境。	

Fluent bit工作流程	

![](/img/in-post/analysis/fluent-bit1.png)	




## 参考资料	


[Fluentd ](https://www.fluentd.org)	

[Fluentd Bit](https://docs.fluentbit.io/manual/)	

[Filebeat Reference](https://www.elastic.co/guide/en/beats/filebeat/current/index.html)	

[Data Lake Services for Real-Time and Streaming Analytics](https://www.xenonstack.com/blog/data-lake-services/)	

[Deploying Kafka With the ELK Stack ](https://dzone.com/articles/deploying-kafka-with-the-elk-stack)	

[Should I use Logstash or Elasticsearch ingest nodes?](https://www.elastic.co/blog/should-i-use-logstash-or-elasticsearch-ingest-nodes)	

[Building an Open Data Platform: Logging with Fluentd and Elasticsearch](https://medium.com/redbox-techblog/building-an-open-data-platform-logging-with-fluentd-and-elasticsearch-4582de868398)	


[Flume学习之路](https://www.cnblogs.com/qingyunzong/category/1211044.html)	

[Flume](https://www.jianshu.com/p/323859671420)	

[Apache Flume 1.7.0 各个模块简介](https://www.cnblogs.com/chenpi/p/7240584.html)	

[大数据技术之_09_Flume学习](https://www.cnblogs.com/chenmingjun/p/10471066.html)	

[Apache NiFi Overview](https://nifi.apache.org/docs/nifi-docs/html/overview.html)