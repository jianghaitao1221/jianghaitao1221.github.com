---
layout:     post
title:      "jenkins在windows slave上执行svn命令报错"
subtitle:   " \"svn: E170001: Can't get username or password\""
date:       2016-08-16 00:00:00
author:     "Jht"
header-img: "img/post-bg-jenkins.png"
catalog: true
tags:
    - jenkins
---

## 现象

```
#jenkins上执行svn命令
svn up
```

报错如下：

```
svn: E170013: Unable to connect to a repository at URL 'svn://svn.xsjme.com/rog2/pirates/trunk'
svn: E170001: Can't get username or password
```

在`windows slave`上执行是没有问题的

## 解决办法

修改`jenkins`服务的权限

- 找到`jenkins`服务
- 右键，选择`属性`
- 点击`登陆`

![img](/img/in-post/jenkins-windows-svn-name-pwd-not-fount/fix.png)

