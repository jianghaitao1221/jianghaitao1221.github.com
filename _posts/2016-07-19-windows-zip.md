---
layout:     post
title:      "windows的zip压缩"
subtitle:   " \"命令行zip压缩的几种方法\""
date:       2016-06-16 00:00:00
author:     "Jht"
header-img: "img/post-bg-zip.jpg"
catalog: true
tags:
    - zip
---



##  winrar

```bat
winrar.exe a -afzip c:\test.zip c:\test.csv #不是rar.exe
#会弹出压缩界面
```

## java

```bat
jar -cMf c:\test.zip c:\test
```

## makecab

```bat 
#压缩一个文件： 
makecab c:/file_name.txt c:/file_name.zip #不支持目录，多个文件需要一个列表，很麻烦
#解压一个文件： 
expand c:/file_name.zip c:/file_name.txt
```
