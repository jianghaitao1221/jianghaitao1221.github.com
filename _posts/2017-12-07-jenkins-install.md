---

layout:     post
title:      "jenkins 2 安装和关键字"
subtitle:   "jenkins学习之一"
date:       2017-12-07 10:00:00
author:     "Jht"
header-img: "img/post-bg-jenkins.png"
catalog: true
tags:
    - jenkins
    - devops
---

## Jenkins 是啥？


Jenkins 是一个独立的开源自动化服务器，可用于自动化各种任务，如构建，测试和部署软件。Jenkins 可以通过本机系统包Docker安装，甚至可以通过安装Java Runtime Environment的任何机器独立运行。

从2005年2月到2016年4月jenkins2发布，jenkins已经经历了10多个年头了，在CI（持续化集成）中扮演着十分重要的地位。随着Docker的诞生，把容器的使用门槛降低更低，把服务部署到容器里面，让服务的部署变得更便捷、灵活。从代码提交到服务的自动部署，测试，销毁服务，在回到代码提交…每一份提交一份代码都能看到最终部署结果，这对敏捷团队来说再好不过了。

### Jekins 2 特点

- CD(Continuous Delivery)
  - Pipeline as Code
- 提高易用度
- 完全兼容1.x 


## 安装


### ubuntu

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

- 日志文件/var/log/jenkins/jenkins.log。检查此文件来排除故障
- /配置文件etc/default/jenkins
- 将Jenkins设置为的守护程序。查看/etc/init.d/jenkins更多详情。
- 访问 http://yourip:8080

### 其他系统

- [下载页面](https://jenkins.io/download/)

## Jenkins 关键字

- Agent
  - Agent通常是一个机器或容器，它连接到Jenkins主机，并在主控器指导时执行任务。
  
- Artifact
  - 在Build或Pipeline 运行期间生成的不可变文件，该文件归档到Jenkins Master上以供用户随后检索。
  
- Build
  - 项目单次执行的结果

- Cloud
  - 提供动态代理 配置和分配的系统配置，例如由Azure VM Agents 或 Amazon EC2插件提供的配置和分配 。

- Core
  - 主要的Jenkins应用程序（jenkins.war）提供了 可以构建Plugins的基本Web UI，配置和基础。

- Downstream
  - 配置Pipeline或项目时被触发作为一个单独的Pipeline或项目的执行的一部分。

- Executor
  - 用于执行由节点上的Pipeline或项目定义的工作的插槽。节点可以具有零个或多个配置的执行器，其对应于在该节点上能够执行多少并发项目或Pipeline。


- Fingerprint
  - 考虑全局唯一性的哈希追踪跨多个Pipeline或项目的工件或其他实体的使用 。


- Folder
  - 类似于文件系统上的文件夹的Pipeline和/或项目的组织容器。

- Item
  - Web UI中的实体对应于：Folder, Pipeline, or Project.

- Job
  - 一个不推荐的术语，与项目同义。

- Label
  -用于分组代理的用户定义的文本，通常具有类似的功能或功能。例如linux对于基于Linux的代理或docker适用于支持Docker的代理。

- Master
  - 存储配置，加载插件以及为Jenkins呈现各种用户界面的中央协调过程。

- Node
  - 作为Jenkins环境的一部分并能够执行Pipeline或项目的机器。无论是the Master还是Agents都被认为是Nodes。

- Project
  - 用户配置的Jenkins应该执行的工作描述，如构建软件等。

- Pipeline
  - 用户定义的连续输送Pipeline模型。

- Plugin
  - 与Jenkins Core分开提供的Jenkins功能扩展。

- Publisher
  - 完成发布报告，发送通知等的所有配置步骤后的构建的一部分。

- Stage
  - stage是Pipeline的一部分，用于定义整个Pipeline的概念上不同的子集，例如：“构建”，“测试”和“部署”，许多插件用于可视化或呈现Jenkins Pipeline状态/进度。

- Step
  - 单一任务从根本上讲，指的是Jenkins 在Pipeline或项目中做了什么。


- Trigger
  - 触发新Pipeline运行或构建的标准。

- Update Center
  - 托管插件和插件元数据的库存，以便在Jenkins内部进行插件安装。

- Upstream
  - 配置的Pipeline或项目，其触发单独的Pipeline或项目作为其执行的一部分。

- Workspace
  - Noede文件系统上的一次性目录， 可以由Pipeline或项目完成工作。在Build或 Pipeline运行完成后，工作区通常会保留原样，除非在Jenkins Master上已经设置了特定的Workspace清理策略。







