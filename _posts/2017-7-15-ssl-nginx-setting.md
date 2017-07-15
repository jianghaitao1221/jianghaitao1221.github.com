---

layout:     post
title:      "ssl证书配置"
subtitle:   "nginx配置文件"
date:       2017-7-15 10:00:00
author:     "Jht"
header-img: "img/certbot-bg.png"
catalog: true
tags:
    - certbot
    - ssl
    - nginx
---

## 配置

 ```bash
 upstream myserver{
    server localhost:8080;
}
server {
    listen       80;
    server_name  myserver.org;
    rewrite     ^   https://$server_name$request_uri? permanent; # 将http访问自动转发给https
}
server {
    listen       443 ssl;
    server_name  myserver.org;

    ssl_certificate      /etc/ssl/myserver.org/fullchain.pem;
    ssl_certificate_key  /etc/ssl/myserver.org/privkey.pem;

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout  5m;

    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    ssl_prefer_server_ciphers   on;
     
    location /api {
        proxy_pass http://myserver;
    }
    
    location / {
       root   /home/myserver/web;
       index index.html;
    }
}


 ```

 ## 参考资料

 - [加强Nginx的SSL安全](http://www.oschina.net/translate/strong_ssl_security_on_nginx)
 - [原文地址](https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html)
 - [Cipherli.st](https://cipherli.st/)