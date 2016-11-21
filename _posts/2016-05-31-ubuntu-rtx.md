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

安装方法：
- wine+rtx
- playonlinux+rtx

`个人推荐第二种`

### 1.wine+rtx

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

#### 解决名字前面两个字是`方框`的问题

点开“应用程序”->“Wine”->“Configure Wine”->“函数库”->在“新增函数库顶替”中，输入“oleaut32.dll”，再点击“添加”->“应用”->“确定”。

#### 解决不能输入中文和消息内容乱码的问题

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

###  第六步 解决不能显示图片的问题


“应用程序”->“wine”->“configure wine”->“函数库”->在“新增函数库顶替”中，输入“riched20.dll和riched30.dll”，再点击“添加”->“应用”->“确定”。


###  第七步 解决自动离线的问题



在登录RTX后在“个人设置”里的“回复设置中”把“自动状态转换”的勾去掉。

## playonlinux+rtx

我测试的时候wine到了1.9.x版本，但是我没装成功。

### 安装playonlinux

```bash
wget -q "http://deb.playonlinux.com/public.gpg" -O- | sudo apt-key add -  
sudo wget http://deb.playonlinux.com/playonlinux_trusty.list -O /etc/apt/sources.list.d/playonlinux.list  
sudo apt-get update  
sudo apt-get install playonlinux

#运行
playonlinux
```
### 配置playonlinux

#### 第一步 配置wine

- 点`Tools`
- 选择`Maqnage Wine versions`
- 选择`amd64`
- 选择相对应`wine`的版本（本文选择的是`1.7.26`）

#### 第二步 创建虚拟盘

- 点击`Configure`
- 选择`new`
- 选择`32`位的虚拟盘
- 选择默认的wine
- 输入虚拟盘的名字

#### 第三步 安装rtx需要的想要组件

- msxml3 
- msxml6 
- ie8 
- vcrun6 
- vcrun2005 
- riched20 
- riched30

#### 第四步 下载rtx

版本`2013`

#### 安装

- 点击`install a program`
- 选择左下角的`install a non-listed program`
- 点击`next`
- 选择`edit or update an existing application`并`next`
- 选择一个虚拟盘，并`next`（查看所有的选择`show virtual drives`）
- 选择`next`
- 选择`32bits windows Installation`并`next`
- 选择`rtxclient2013formal.exe`进行安装

忽略错误`RTXShIMwnu.dll`注册失败的错误。










