---

layout:     post
title:      "Let's Encrypt(certbot)更新"
subtitle:   "自动更新"
date:       2017-12-15 09:00:00
author:     "Jht"
header-img: "img/certbot-bg.png"
catalog: true
tags:
    - certbot
---

## 自动化定期更新证书

### 证书续期任务

```bash
sudo vim /etc/systemd/system/letsencrypt-renew.service

#内容如下
[Unit]
Description=Lets Encrypt renewal

[Service]
Type=simple  
ExecStart=/usr/bin/letsencrypt renew               #renew的方式取决于你第一次申请的方式
ExecStartPost=/bin/systemctl reload nginx.service  #在/etc/letsencrypt/renewal中对应的域名目录下
                                                   #查看renew配置
#测试 
sudo systemctl start letsencrypt.service
```


### 自动执行

```bash
sudo vim /etc/systemd/system/letsencrypt-renew.timer
#内容如下

[Unit]
Description=Monthly renewal of Lets Encrypts certificates

[Timer]
OnCalendar=*-*-2 3:00:00                          #webroot方式，每个月2号更新
Unit=letsencrypt-renew.service

[Install]
WantedBy=timers.target

#启动
sudo systemctl enable letsencrypt-renew.timer
sudo systemctl start letsencrypt.timer

#查看
sudo systemctl is-enabled letsencrypt-renew.timer
sudo systemctl list-timers
```