---
layout:     post
title:      "搭建本地ubuntu集群"
subtitle:   " \"ubuntu虚拟机集群设置固定IP并访问外网\""
date:       2021-08-12 00:00:00
author:     "Jht"
header-img: "img/hyperv-bg.jpg"
catalog: true
tags:
    - ubuntu
    - hyper-v
---

## 创建虚拟机

#### 1. 打开Hyper-V管理器，这里选择快速创建，点击它。

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-1.png)

#### 2. 这里建议选择左边的系统，并点击创建虚拟机。

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-2.png)

#### 3.开始下载镜像并创建虚拟机

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-3.png)

#### 4. 虚拟机创建好后，点击连接虚拟机，然后会转到下面这个界面，点击启动（关机后启动也这么操作）

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-4.png)

#### 5. 启动后就是Ubuntu 第一次启动的初始化设置，选择相关设置，然后点击继续（Next）。

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-5.png)


![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-6.png)

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-7.png)

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-8.png)

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-9.png)

#### 6. 配置完成后，选择连接（这是启动后连接）。

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-10.png)

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-11.png)

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-create-12.png)

## 设置静态IP

### 命令行

Ubuntu 18.04之后 采用netplan作为网络配置管理，不需要重启系统

```bash
# 第一步
sudo vim /etc/netplan/50-cloud-init.yaml
# 第二步
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:   #配置的网卡名称
      dhcp4: no    #dhcp4关闭
      dhcp6: no    #dhcp6关闭
      addresses: [172.26.39.156/24]   #设置本机IP及掩码
      gateway4: 172.26.39.1  #设置网关
      nameservers:
          addresses: [172.26.39.1, 8.8.8.8,宿主机的DNS]   #设置DNS，或宿主机的DNS
# 第三步
sudo  netplan apply 
```
#### DNS配置

宿主机的DNS，网关等。最好是宿主机的DNS

#### 注意点

- 1.以上配置文件共11行，其中第2，3，6，7四行可以不写，测试过没有这四行，网络也能工作正常，第5行的ens33为虚拟网卡，可以使用ifconfig -a查看本机的网卡。
- 2.配置文件里在冒号：号出现的后面一定要空一格，不空格则在运行netplan apply时提示出错。
- 3.关键之关键是看清配置总共分为五个层次，逐层向后至少空一格，

```
第一层－network:
第二层－－ ethernets:
第三层－－－ ens33:
第四层－－－－addresses:  [172.26.39.156/24]
第四层－－－－gateway4:  172.26.39.1
第四层－－－－nameservers:
第五层－－－－－addresses: [172.26.39.1, 8.8.8.8]
```

### 图形界面设置

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-1.png)

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-2.png)

## 和宿主机互联并连接外网

### 新建内部网络虚拟交换机

#### 创建

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-3.png)


#### 创建成功

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-4.png)

#### 应用

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-5.png)


### 修改虚拟网卡IP地址

打开控制面板->网络和共享中心->更改适配器设置;

找到刚刚在hyper-v上新增的虚拟网络交换机（WSL），右键选择属性 --> 选择如下IPv4协议 --> 设置如下IP地址和子网掩码，其他可留空，IP地址必须是172.26.39.1，和ubuntu上设置的网段，需保持一致。

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-6.png)

以上设置好后就可以在本地物理机访问虚拟机了，如下我们ping 下刚才的172.26.39.156，可见是可以ping得通的，这时就可以使用xshell等终端进行连接操作了。

### 虚拟机访问外网

#### 修改注册表默认的共享网络IP地址

##### 手动修改注册表

HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-8.png)


##### bat脚本修改注册表

```bat
@echo off
set /p q=Pleasl input ShareIP [192.168.173.1]:
reg add "HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters" -v ScopeAddress -d %q% -f

reg add "HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters" -v ScopeAddressBackup -d %q% -f

timeout /t 10 /nobreak
```
以管理员身份运行 并输入要变成的IP

#### 共享网络

若在虚拟机上有需要访问外网的操作，则在控制面板->网络和共享中心->更改适配器设置中，选中此时物理机使用的网卡，右键属性点击共享选项，如下选中复选框，再选择hyper-v上我们新建的网络，点击确定，这样操作之后我们的虚拟机就可以共享访问到外部的网络了。


![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-7.png)

上一步点击确定后会弹出下面的对话框，会提示虚拟网卡的IP地址将被设置成172.26.39.1，这个就是前文提到的他是默认的共享网络IP地址，由于之前我本身就设置的172.26.39.1，所以此处点我们不再需要做任何修改

![第一步](http://jianghaitao1221.github.io/img/in-post/hyperv/hyperv-network-9.png)

 点击“是”后，会短暂失去连接，重新使用Xshell等工具连接虚拟机，此时虚拟机已经可以连接外网

 我们可以

 ```bash
  ping www.baidu.com

 ```

 如果ping不通，选中物理机使用的网卡，关闭共享，再打开共享，再试试能不能ping通。

## 参考资料

- [Hyper-V虚拟机配置内部网络固定IP并连接外网](https://www.cnblogs.com/kasnti/p/11727755.html)
- [Hyper-V虚拟机设置固定IP](https://www.jianshu.com/p/1e6822bcf8ae)
- [修改共享网络IP地址](https://www.cnblogs.com/feiquan/p/10802174.html)


