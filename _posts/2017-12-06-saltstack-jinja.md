---

layout:     post
title:      "saltstack之jinja"
subtitle:   "saltstack学习之十八"
date:       2017-12-06 10:00:00
author:     "Jht"
header-img: "img/saltstack-bg.jpg"
catalog: true
tags:
    - saltstack
    - devops
---

## Jinja 是啥？


Jinja2 是一个现代的，设计者友好的，仿照 Django 模板的 Python 模板语言。 它速度快，被广泛使用，
并且提供了可选的沙箱模板执行环境保证安全:

```bash
<title>{% block title %}{% endblock %}</title>
<ul>
{% for user in users %}
  <li><a href="{{ user.url }}">{{ user.username }}</a></li>
{% endfor %}
</ul>
```
### 特性

- 沙箱中执行
- 强大的 HTML 自动转义系统保护系统免受 XSS
- 模板继承
- 及时编译最优的 python 代码
- 可选提前编译模板的时间
- 易于调试。异常的行数直接指向模板中的对应行。
- 可配置的语法


## 使用

SaltStack 是使用 YAML 语言来将 SLS 文件解释成它自己可以识别的内容。默认的模板引擎是 Jinja。

### 条件

```bash
{% if grains['os_family'] == 'RedHat' %}
apache: httpd
git: git
{% elif grains['os_family'] == 'Debian' %}
apache: apache2
git: git-core
{% endif %}
```

### 循环

```bash
{% for DIR in ['/dir1','/dir2','/dir3'] %}
{{ DIR }}:
  file.directory:
    - user: root
    - group: root
    - mode: 774
{% endfor %}
```

### 执行模块

比如当我们需要对多台服务器做一些 Apache 服务配置时，由于每台服务器信息不一样（比如 IP 不同），
如果为每台服务器去创建 SLS 文件就不太合理，而通过 Jinja 模板则可以用生成变量→读取变量的方式来为每个服务器设置应有的信息。

```bash
apache-service:
  file.managed:
    - name: /etc/httpd/conf/httpd.conf
    - source: salt://files/httpd.conf
    - user: root
    - group: root
    - mode: 644
    - template: jinja                       #增加这行表示开启模板
    - defaults:                             #下面设定变量的值
      HOST: {{ grains['fqdn_ip4'][0] }}     #[fqdn_ip4]代表的是IP，由于grains查询输出的是列表，会有多个值，需要加上[0]代表取第一个值
      PORT: 8181                            #监听的端口

  service.running:
    - name: httpd
    - enable: True
    - reload: True
    - watch:
      -file: apache-service
```

## 参考文档

[Jinja 文档](http://docs.jinkan.org/docs/jinja2/index.html)

[UNDERSTANDING JINJA](https://docs.saltstack.com/en/latest/topics/jinja/index.html)

[Saltstack Jinja](https://docs.saltstack.com/en/getstarted/config/jinja.html)






