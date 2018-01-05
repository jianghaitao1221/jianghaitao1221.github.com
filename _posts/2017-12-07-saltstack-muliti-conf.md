---

layout:     post
title:      "saltstack之多目录配置"
subtitle:   "saltstack学习之十九"
date:       2017-12-07 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## 多目录配置


随着环境的越来越多，（例如，dev,intranet,test,prod等），我们需要对conf文件进行提取公共逻辑，需要分环境。

### 配置示例

```bash
file_roots:
  base:
    - /srv/conf/common/salt # common里只有sls
    - /srv/conf/intranet/salt
#sls优先级：common的优先级高,会先搜索common，在搜索intranet，当common有了，会以common中为准
#===========================================================
pillar_roots:
  base:
    - /srv/conf/common/pillar # common里只有公共pillar
    - /srv/conf/intranet/pillar #top.sls 在各个环境的配置文件中
#pillar优先级：intranet会覆盖common
```
## 官方文档

[FILE SERVER CONFIGURATION](https://docs.saltstack.com/en/latest/ref/file_server/file_roots.html)






