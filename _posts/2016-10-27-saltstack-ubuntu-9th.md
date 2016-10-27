---
layout:     post
title:      "saltstack之file.managed"
subtitle:   "\"saltstack学习九\""
date:       2016-10-27 04:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - saltstack
    - devops
---



## file.managed

文件管理，可以使用模板文件。只有file模块里才可以使用template。

`salt.states.file.managed(name, source=None, source_hash='', user=None, group=None, mode=None, template=None, makedirs=False, dir_mode=None, context=None, 
replace=True, defaults=None, env=None, backup='', show_diff=None, show_changes=True, create=True, contents=None, contents_pillar=None, contents_grains=None, 
contents_newline=True, contents_delimiter=':', allow_empty=True, follow_symlinks=True, check_cmd=None, skip_verify=False, **kwargs)`


```bash
/etc/nginx/nginx.conf:
  file.managed:
    - source:
      - salt://dev/nginx/nginx.conf
```
### name

同步到minion上的文件路径和文件

### source

要同步的源文件。可以是master上的文件，可以是`http(s)://, ftp://，s3：//` 

`s3中国区有问题`


### source_hash

hash值可以是：

- hash字符串
- hash文件

#### hash类型


| 类型        | 长度          |
| ------------- |:-------------:|
|sha512    |128 | 
|sha384    |96  |   
|sha256    |64  |   
|Lsha224   |56  | 
|sha1      |40  |   
|md5       |32  |  

#### hash文件

```bash
tomdroid-src-0.7.3.tar.gz:
  file.managed:
    - name: /tmp/tomdroid-src-0.7.3.tar.gz
    - source: https://launchpad.net/tomdroid/beta/0.7.3/+download/tomdroid-src-0.7.3.tar.gz
    - source_hash: https://launchpad.net/tomdroid/beta/0.7.3/+download/tomdroid-src-0.7.3.hash
```

#### hash字符串

```bash
tomdroid-src-0.7.3.tar.gz:
  file.managed:
    - name: /tmp/tomdroid-src-0.7.3.tar.gz
    - source: https://launchpad.net/tomdroid/beta/0.7.3/+download/tomdroid-src-0.7.3.tar.gz
    - source_hash: md5=79eef25f9b0b2c642c62b7f737d4f53f
```

#### 不指定hash

`当不指定source_hash时，当源文件变了，如果本地缓存了之前的版本，那么新文件是不会被更新的`

[跳过hash验证](#skipverify)


### user

文件属主

### group

文件属组

### mode

文件权限，如：644, 0775, 4664，在windows上不支持

### template

文件模板类型，支持：

- cheetah
- genshi
- jinja
- mako
- py
- wempy

### makedirs

默认：False,如需要管理的文件父目录不存，执行失败，设置为True，会创建父目录。


### dir_mode

目录权限

### replace

默认:True，设为False将不替换文件内容，权限和属主将被修改。


### context

覆盖默认的上下文参数传给模板

### defaults

默认上下文参数传给模板

### backup

备份功能，有改动才做备份，backup: minion 会备份到minion下的/var/cache/salt/minion/backup_files。
文件名为 [name](#name).bak 

### show_diff

废弃: 请用 show_changes

###  show_changes

显示新文件和就文件的不同， 设置为False，将会返回一个boolean

### create

默认：True，如果设置为False，只有当文件以存在于minion上的时候，才会管理文件

### contents

指定文件内容，不能使用source，忽略hash和模板

```bash
/opt/a.txt:
  file.managed:
    - contents:
      - This is line 1
      - This is line 2

/opt/a.txt:
  file.managed:
    - contents: |
        This is line 1
        This is line 2
#会在/opt生成一个a.txt文件

cat a.txt
#结果
This is line 1
This is line 2

```

### contents_pillar

和`contents`类似，可以从pillar中取值

```bash
/home/deployer/.ssh/id_rsa:
  file.managed:
    - user: deployer
    - group: deployer
    - mode: 600
    - contents_pillar: userdata:deployer:id_rsa
#查看文件
cat /home/deployer/.ssh/id_rsa
#结果
userdata:
  deployer:
    id_rsa: |
        -----BEGIN RSA PRIVATE KEY-----
        MIIEowIBAAKCAQEAoQiwO3JhBquPAalQF9qP1lLZNXVjYMIswrMe2HcWUVBgh+vY
        U7sCwx/dH6+VvNwmCoqmNnP+8gTPKGl1vgAObJAnMT623dMXjVKwnEagZPRJIxDy
        B/HaAre9euNiY3LvIzBTWRSeMfT+rWvIKVBpvwlgGrfgz70m0pqxu+UyFbAGLin+
        GpxzZAMaFpZw4sSbIlRuissXZj/sHpQb8p9M5IeO4Z3rjkCP1cxI
        -----END RSA PRIVATE KEY-----
```

### contents_grains

和`contents_pillar`类似，从grains取值

```bash
write_os:
  file.managed:
    - name: /opt/os
    - contents_grains: os

```

### contents_newline

默认：True
如果文件包含二进制数据，这个选项会被忽略。
当为True的时候，使用`contents, contents_pillar, or contents_grains`的时候文件末尾会添加换行符。

`我没试出来`


### contents_delimiter

待完成。

### allow_empty

默认：True，设置为False，`contents_pillar` 和 `contents_grains` 为空的时候会报错

### skip_verify

默认：False，设置为True，sources指定为http://, https://, ftp://，会忽略source_hash

## 官方文档

[官方文档](https://docs.saltstack.com/en/latest/ref/states/all/salt.states.file.html#salt.states.file.managed)
