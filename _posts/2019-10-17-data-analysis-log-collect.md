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