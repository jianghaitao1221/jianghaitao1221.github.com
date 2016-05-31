---
layout:     post
title:      "ubuntu16.04下完美安装RTX"
subtitle:   " \"解决不能输入中文，乱码等问题\""
date:       2016-05-31 00:00:00
author:     "Jht"
header-img: "img/home-bg-computer.jpg"
catalog: true
tags:
    - ubuntu
    - linux
---

##  ubuntu16.04下完美安装RTX

### 第一步 安装wine

```bash
sudo apt-get install wine
```
### 第二步 下载 winetricks

```bash
wget  http://kegel.com/wine/winetricks
```
### 第三步 下载 winetricks

#### msxml3 需要手动安装
```bash
winetricks msxml3 
```
运行上面的命令，会给出提示，去相应的网页下载。当时的可用连接如下。

[msxml3](http://download.cnet.com/Microsoft-XML-Parser-MSXML-3-0-Service-Pack-7-SP7/3000-7241_4-10731613.html)

#### 安装其余的依赖

```bash
winetricks  gdiplus riched20 riched30 ie6 vcrun6 vcrun2005sp1
```

### 第三步 下载 rtx

#### rtx官方2015版本

[下载](http://rtx.tencent.com/rtx/download/index.shtml)

### 第四步 安装

安装rtx，和windows一样，直接下一步就行。最后会有个提示，RTXShlMenu.dll的错误，忽略。安装完成。

### 第五步 解决乱码和不能输入中文的问题

```bash
gedit Desktop/Tencent\ RTX.desktop #在env 后面加上"XMODIFIERS=@im=fcitx  QT_IM_MODULE=fcitx  GTK_IM_MODULE=fcitx LANG='zh_CN.UTF-8' LC_ALL='zh_CN.UTF-8'"
```

```
[Desktop Entry]
Name=Tencent RTX
Exec=env XMODIFIERS=@im=fcitx  QT_IM_MODULE=fcitx  GTK_IM_MODULE=fcitx LANG='zh_CN.UTF-8' LC_ALL='zh_CN.UTF-8' WINEPREFIX="/home/xxx/.wine" wine C:\\\\windows\\\\command\\\\start.exe /Unix /home/xxx/.wine/dosdevices/c:/users/Public/Desktop/Tencent\\ RTX.lnk
Type=Application
StartupNotify=true
Path=/home/xxx/.wine/dosdevices/z:/home/xxxx/User/rtx
Icon=9838_RTX.0
```

###  第六步 解决自动离线的问题



在登录RTX后在“个人设置”里的“回复设置中”把“自动状态转换”的勾去掉。






