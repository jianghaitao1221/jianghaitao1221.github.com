---
layout:     post
title:      "用markdown画时序图"
subtitle:   " \"能画简单的时序图\""
date:       2016-08-16 00:00:00
author:     "Jht"
header-img: "img/home-bg-md.jpg"
catalog: true
tags:
    - markdown
---

## 语法

### Participants

`Participant` 可以隐式的被定义。

```
    ```sequence #标识时序图
        title:铁达尼号 #标题
        participant 肉丝 #角色Actor
        participant 夹克
        肉丝->>夹克: 你瞅啥！ #消息
        夹克-->>肉丝: 瞅你咋的！
    ```
#下面这样写也行
    ```sequence
        肉丝->>夹克: 你瞅啥！
        夹克-->>肉丝: 瞅你咋的！
    ```
```

`participant`的书写顺序决定了`角色`在图中的顺序

```
    ```sequence
        participant 夹克
        participant 肉丝
        肉丝->>夹克: 你瞅啥！
        夹克-->>肉丝: 瞅你咋的！
    ```
```
![img](/img/in-post/markdown-sequence/seq_p_1.jpg)


```
    ```sequence
        肉丝->>夹克: 你瞅啥！
        夹克-->>肉丝: 瞅你咋的！
    ```
```
![img](/img/in-post/markdown-sequence/seq_p_2.jpg)

### Messages

```
[Actor][Arrow][Actor]:消息内容
```

#### Arrow

- ->   实线不带箭头（实心箭头）
- ->>  实线带箭头
- -->  虚线不带箭头（实心箭头）
- -->> 虚线带箭头


```
    ```sequence
        participant 夹克
        participant 肉丝
        肉丝->夹克: 你瞅啥！
        夹克->>肉丝: 瞅你咋的！
        肉丝-->夹克: 你瞅啥！！！
        夹克-->>肉丝: 瞅你咋的！！！
    ```
```
![img](/img/in-post/markdown-sequence/seq_p_3.jpg)

### Notes

添加备注

- Note left of [Actor]
- Note right of [Actor]
- Note over of [Actor]
- Note over of [Actor1],[Actor2]

```
    ```sequence
            participant 夹克
            participant 肉丝
            Note left of 肉丝:平静的说
            肉丝->夹克: 你瞅啥！
            Note right of 夹克:挑着眉说
            夹克->>肉丝: 瞅你咋的！
            Note over 夹克:瞪着眼说
            肉丝-->夹克: 你瞅啥！！！
            Note over 夹克,肉丝:互相指着对方说
            夹克-->>肉丝: 瞅你咋的！！！
    ```
```

![img](/img/in-post/markdown-sequence/seq_p_4.jpg)

## 局限性

这些是时序图最基本的东西，大部分`markdown`产品支持，某些产品可能支持更高级的语法。

好多在线工具可以用来画时序图，如：马克飞象。 


