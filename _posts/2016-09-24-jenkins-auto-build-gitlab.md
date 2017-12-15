---
layout:     post
title:      "jenkins使用gitlab自动化部署"
subtitle:   "\"Build when a change is pushed to GitLab.\""
date:       2016-09-24 00:00:00
author:     "Jht"
header-img: "img/post-bg-jenkins.png"
catalog: true
tags:
    - jenkins
    - gitlab
---



## Build when a change is pushed to GitLab

`gitlab`在`push`以后自动触发`jenkins job`进行`build`

## 插件

- `Gitlab Hook Plugin`
- `GitLab Plugin`

## 配置

### 设置SCM

- `new item`
- 在`Source Code Management`中选定`git`
- 配置`git`

### 设置Triggers

![img](/img/in-post/jenkins-auto-build-gitlab/gitlab-hook.png)

### 设置Webhooks

![img](/img/in-post/jenkins-auto-build-gitlab/webhook.png)

## 提交代码

`Push`代码，`job`就能自动运行。


