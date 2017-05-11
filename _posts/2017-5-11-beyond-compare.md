---
layout:     post
title:      "Beyond Compare绿色版"
subtitle:   "\"制作方法（4.1.19）\""
date:       2017-5-31 08:00:00
author:     "Jht"
header-img: "img/util-bg.png"
catalog: true
tags:
    - Beyond Compare
---

## Beyond Compare

`Beyond Compare`是一款专业的文件比对神器。

### 安装

[Beyond Compare 4.1.9](http://www.scootersoftware.com/BCompare-zh-4.1.9.21719.exe)

### 注册码

- 购买官方正版；
- 使用网上流传的注册机，先patch，再算号注册

## 配置优化

重点在于`BCState.xml`

### 第一步 

去掉自动检测更新提示——打开`BCState.xml`，删除部分如下：

```xml
<TCheckForUpdatesState>
.....
</TCheckForUpdatesState>
```

### 第二步

- 打开`Beyond Compare`
- 点击工具栏“视图”
- 去掉“显示网络资源”的小勾

### 第三步

配置好以后，把`BCState.xml`改为只读属性就好。

### 备注

估计先优化，在注册也OK。
