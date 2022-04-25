---
layout:     post
title:      "git ssh key"
subtitle:   " \"使用 ssh key 克隆代码\""
date:       2022-04-21 00:00:00
author:     "Jht"
header-img: "img/post-bg-git.jpg"
catalog: true
tags:
    - git
    - ssh
---

## 概述

使用 ssh key 进行 git 操作

## 生成 ssh key

### 使用 Ed25519 算法生成你的 SSH 密钥

常见的 SSH 登录密钥使用 RSA 算法。RSA 经典且可靠，但性能不够理想。

只要你的服务器上 OpenSSH 版本大于 6.5（2014 年的古早版本），就可以利用 Ed25519 算法生成的密钥对，减少你的登录时间。如果你使用 SSH 访问 Git，那么就更值得一试。

Ed25519 的安全性在 RSA 2048 与 RSA 4096 之间，且性能在数十倍以上。

### 准备工具
你需要用到 ssh-keygen，它是 OpenSSH 的组件，在 Linux 和 macOS 中一般都自带了。

如果你使用 Windows，安装 Git for Windows 时会一并安装 OpenSSH 到系统中。建议 Windows 用户使用 Git Bash 完成文中操作。

##  在 git 上配置 ssh key

### 生成密钥

```bash
cd ~/.ssh
ssh-keygen -t ed25519 -C "xxx@xx.com"
# 指定名字
ssh-keygen -t ed25519 -f {YOUR FILR NAME}  -C "xxx@xx.com"
```
命令参数：
– t 指定金鑰型別，預設是 rsa ，可以省略。
-C 設定註釋文字，如mail，這邊的mail需要是註冊Gitlab的那個mail。
-f 指定金鑰檔案儲存檔名。

### 本地配置

### linux

```bash
vi ~/.ssh/config
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes

Host gitlab.com
  HostName gitlab.com
  User git
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes
```
### windows

也一样，放在你当前用户的 .ssh 目录下

### github

[新增 SSH 密钥到 GitHub 帐户](https://docs.github.com/cn/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

### gitlab

和 github 类似

