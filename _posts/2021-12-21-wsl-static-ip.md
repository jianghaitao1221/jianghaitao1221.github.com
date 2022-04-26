---
layout:     post
title:      "wsl static IP"
subtitle:   " \"给 wsl2 的 ubuntu 分配固定 IP\""
date:       2021-12-21 00:00:00
author:     "Jht"
header-img: "img/post-bg-wsl2.jpg"
catalog: true
tags:
    - wsl2
---

## 概述

Windows 每次重启 wsl2 的 IP 都会变化。如果在上面部署服务，就会比较烦。

##  思路

- 设置 wsl 的IP
- 修改 DNS
- 开启 sshd 
- 在 Windows 分配了一个同网段的 IP
- 需要用管理员身份运行
- 每次重启之后，需要运行一次

### 修改 wsl2 默认 DNS

修改虚拟网卡地址 [修改虚拟网卡IP地址](http://jianghaitao1221.github.io/2021/08/12/hyperv-network/#%E4%BF%AE%E6%94%B9%E8%99%9A%E6%8B%9F%E7%BD%91%E5%8D%A1ip%E5%9C%B0%E5%9D%80)

```bash
# 修改 /etc/wsl.conf
sudo vi /etc/wsl.conf
# 添加
[network]
generateResolvConf=false
# 保存

# 删除原来的 /etc/resolv.conf
rm /etc/resolv.conf

# 创建新的 /etc/resolv.conf
echo 'nameserver 172.26.39.1' > /etc/resolv.conf

```

### 脚本

```bat
@echo off
setlocal enabledelayedexpansion

wsl -d Ubuntu-20.04 -u root ip addr del $(ip addr show eth0 ^| grep 'inet\b' ^| awk '{print $2}' ^| head -n 1) dev eth0
echo delete eth0
wsl -d Ubuntu-20.04 -u root ip addr add 172.26.39.106/24 broadcast 172.26.39.255 dev eth0
echo set wsl ip success: 172.26.39.106
wsl -d Ubuntu-20.04 -u root ip route add 0.0.0.0/0 via 172.26.39.1 dev eth0
echo ip route
wsl -d Ubuntu-20.04 -u root touch /etc/resolv.conf
echo touch /etc/resolv.conf
wsl -d Ubuntu-20.04 -u root echo nameserver 172.26.39.1 ^> /etc/resolv.conf
echo set /etc/resolv.conf
wsl -d Ubuntu-20.04 -u root service ssh start
echo start sshd
powershell -c "Get-NetAdapter 'vEthernet (WSL)' | Get-NetIPAddress | Remove-NetIPAddress -Confirm:$False; New-NetIPAddress -IPAddress 172.26.39.1 -PrefixLength 24 -InterfaceAlias 'vEthernet (WSL)'; Get-NetNat | ? Name -Eq WSLNat | Remove-NetNat -Confirm:$False; New-NetNat -Name WSLNat -InternalIPInterfaceAddressPrefix 172.26.39.0/24;"
pause
```