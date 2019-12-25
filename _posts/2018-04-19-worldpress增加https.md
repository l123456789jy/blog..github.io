---
layout:     post
title:      "worldpress增加https"
subtitle:   ""
date:       2018-04-19 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - worldpress
---



1.我的域名申请的阿里云的，首先去阿里云申请免费的证书
2.申请完毕之后，在自己的域名管理中会有证书管理，选择ssl证书
3.选择nginx配置
4.nginx的位置在`/usr/local/nginx/conf`目录位置下面，我们先创建`cert`文件夹，然后在阿里云上的证书管理将证书下载下来，存放到当前的`cert`目录下面
5.进入到`vhost`目录下面将当前域名.con文件拷贝出来配置
6.

    server {
        listen 443;
        server_name localhost;
        ssl on;
        root html;
        index index.html index.htm;
        ssl_certificate   证书.pem;
        ssl_certificate_key  证书.key;
        ssl_session_timeout 5m;
        ssl_ciphers 证书秘钥;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;
        location / {
            root html;
            index index.html index.htm;
        }
    }






7.执行`lnmp restart`重启服务器即可
8.但是这样，当我们输入http的域名开头的时候并不会转向https,所以我们还需要做如下配置

```java
server {
    listen 80;
    server_name www.liujingyuan.top ;
    rewrite ^(.*)$  https://$host$1 permanent;
}
```
意思就是监听80端口，然后冲定向到https即可！
