---

layout:     post
title:      "LuaIde"
subtitle:   "比较好用的lua插件"
date:       2017-10-24 10:00:00
author:     "Jht"
header-img: "img/lua-bg.jpg"
catalog: true
tags:
    - lua
---

## luaide

luaIde 是基于vscode开发的一款用于lua语言开发者使用的插件.

### 功能

- 智能提示
- 跳转 
- 调试
- 收费版功能更丰富

`详见版本介绍`

### 版本

- [免费版](https://github.com/k0204/LuaIde/)
- [收费版](https://marketplace.visualstudio.com/items?itemName=kangping.luaide)

### 安装

#### 免费版

- [本地下载](http://jianghaitao1221.github.io/attach/kangping.luaide-0.3.7.zip)
- [github](https://github.com/k0204/LuaIde/)

将插件放到 `C:\Users\username\.vscode\extensions`

#### 收费版

- `ctrl+shift+x` 搜索 `luaide`

### 配置

需要禁用其他`lua debug 插件`

#### 免费版

- 文件->首选项->设置
- `用户设置`下`"extensions.autoUpdate": false`
- 或者修改 `C:\Users\username\.vscode\extensions\kangping.luaide-0.3.7\package.json`的`name`和`displayName`
- `工作区域`下` "luaide.scriptRoots": ["C:/Users/username/work/xxxxx/luadir"]` 可以配置多个目录

#### 收费版

- `工作区域`下` "luaide.scriptRoots": ["C:/Users/username/work/xxxxx/luadir"]` 可以配置多个目录

#### 其他配置项

[配置项](http://www.jianshu.com/p/f850e5276977)

