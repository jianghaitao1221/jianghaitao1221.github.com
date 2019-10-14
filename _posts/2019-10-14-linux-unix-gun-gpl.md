---
layout:     post
title:      "Linux系列（二）"
subtitle:   " \"啥是Linux\""
date:       2019-10-14 01:00:00
author:     "Jht"
header-img: "img/post-bg-linux.jpg"
catalog: true
tags:
    - linux
---

## Linux是啥

- Linux是一个符合POSIX标准的kernel。
- GNU/Linux是个OS。
- GNU是理查德·斯托曼发起的一项计划，该计划就是要建立完全自由的操作系统。
- GPL是GNU General Public License的缩写，意味着他是开源的。

## Linux的怎么来的呢

### 绕不过去的Unix

UNIX操作系统诞生于20世纪60年代后期。AT＆T的贝尔实验室发布了一个名为Unix的操作系统，用C编写，可以更快地修改，接受和移植。
它始于贝尔实验室的Ken Thompson领导下的一个项目。它继续成为最广泛使用的操作系统。Unix是一种专有的操作系统。
它的商标权由国际开放标准组织所拥有，只有符合单一UNIX规范的UNIX系统才能使用UNIX这个名称，否则只能称为类UNIX。

在其鼎盛时期，UNIX迅速被采用并成为大学的标准操作系统。

Unix最开始从贝尔实验室开发出来并不是处于任何商业目的，是免费的的。后来随着Unix的影响越来越大，AT&T公司想赚钱，就不开源了。

后来UNIX发展主要的两个分支，
- AT&T的System V版本
- 加州大学伯克利分校支持的BSD版本。（从法律意义上讲，BSD不能称为unix）

#### 特性

- 多用户
- 多任务
- 支持多种处理器架构
- 分时操作系统
  
#### Unix大事记

- 1969 Ken Thompson用汇编为DEC PDP-7小型机编写出Unix的第一个版别。
- 1970 操作体系被命名为Unics，后来演化成了今日的Unix。
- 1971 Unix被移植到PDP-11小型机，Unix程序员手册第一版出书。
- 1972 Dennis Ritchie创造C言语。
- 1973 Unix引进管道的概念，Unix被用C言语从头改写。
- 1974 《Unix分时体系》在ACM的杂志上宣告，Unix开端遭到广泛重视。
- 1978 Bill Joy发布BSD Unix。
- 1983 AT&T发布Unix System V，根据此，IBM推出了AIX，HP推出HP-UX。
- 1987 AT&T和Sun宣告一同开发一个Unix。Andrew Tanenbaum开宣告Minix。
- 1988 Unix战役迸发，DEC、HP、IBM等建立Open Software Foundation (OSF)，AT&T等建立Unix International。
- 1989 System V Release 4 (SVR4)发布, System V、BSD、SunOS和Xenix得以一致。
- 1991 Sun根据SVR4发布Solaris，Linux Torvalds以Minix为根底开宣告Linux。
- 1993 AT&T把Unix System实验室和Unix的版权出售给Novell，不久Novell把Unix商标转售给X/Open Group。同年，Microsoft发布Windows NT。
- 1996 X/Open与Open Software Foundation合并为The Open Group。

## Linux的诞生

### 1.拥有一个好老师

因为UNIX私有化了，塔能鲍姆教授为了能在课堂上教授学生操作系统运作的实务细节，决定在不使用任何AT&T的源代码前提下，自行开发与UNIX兼容的操作系统，以避免版权上的争议。他以小型UNIX（mini-UNIX）之意，将它称为MINIX。他是开源的。

### 2.林纳斯·托瓦兹

后来林纳斯·托瓦兹在吸收了MINIX精华的基础上，所以自己写了一个内核，取名为Linux。

### 3.GNU计划

GNU是理查德·斯托曼发起的一项计划，该计划就是要建立完全自由的操作系统。GNU是“GNU's Not Unix”的递归缩写。
从1983年开始的GNU计划致力于开发一个自由并且完整的类Unix操作系统，包括软件开发工具和各种应用程序。到1991年Linux内核发布的时候，GNU已经几乎完成了除了系统内核之外的各种必备软件的开发。

#### 组成

- GNU编译器套装（GCC）
- GNU的C库（glibc）
- 以及GNU核心工具组（coreutils）
- GNU除错器（GDB）
- GNU二进制实用程序（binutils）
- GNOME桌面环境

#### GNU/Linux（Linux操作系统）

在林纳斯·托瓦兹和其他开发人员的努力下，GNU组件可以运行于Linux内核之上。整个内核是基于GNU通用公共许可。但是Linux内核并不是GNU计划的一部分。

#### Linux标志

Linux的标志是企鹅，叫Tux。 Linux的注册商标是林纳斯·托瓦兹所有的。
在1996年，一个律师开始向各个Linux发布商发信，声明他拥有Linux商标的所有权，并且要求各个发布商支付版税，这些发行商集体进行上诉，要求将该注册商标重新分配给林纳斯·托瓦兹。

## 最后

我们平时说的Linux，准确的说是“GNU/Linux”。

