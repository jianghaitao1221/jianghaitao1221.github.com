---
layout:     post
title:      "jenkins定义环境变量"
subtitle:   "\"环境变量在build step间传递，环境变量在job间传递\""
date:       2016-10-17 01:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - jenkins
---



## build step间传递变量


### 插件

- `EnvInject plugin`插件


### 配置

### Configure System

- 点击`Manage Jenkins`
- 选择`Configure System`
- 查找`Global properties`
- 勾选`Prepare jobs environment`
- 设置

### SCM结帐后注入变量

- 进入`job`页面
- `Build Environment`
- 勾选`Inject environment variables to the build process`或`Inject passwords to the build as environment variables`

###  build step注入变量

- `job`页面
- `Build`
- 点击`Add build step`
- 选择`Inject environment variable`


### 官方文档

[地址](https://wiki.jenkins-ci.org/display/JENKINS/EnvInject+Plugin)

## job间传递变量

### 插件

- `Parameterized Trigger Plugin`插件

### 配置

- 进入`job`页面
- `Post-build Actions`
- 点击`Add post-build action`
- 选择`Trigger parameterized build on other projects`

![img](/img/in-post/jenkins-env-value/parameterized-trigger-plugin.png)

### 官方文档

[官方文档](https://wiki.jenkins-ci.org/display/JENKINS/Parameterized+Trigger+Plugin)


## 据条件判断来决定是否触发下一个Job

### 插件

- `Flexible Publish Plugin`

### 官方文档

[官方文档](https://wiki.jenkins-ci.org/display/JENKINS/Flexible+Publish+Plugin)