---
layout:     post
title:      "CentOS 一键安装Caddy + Aria2 + AriaNg实现离线下载"
subtitle:   ""
date:       2018-10-08 18:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - VPS
---



##### 功能
- 支持HTTP/HTTPS/FTP/BT/磁力链接等离线下载，断点续传等
- 文件管理、视频在线播放（支持MP4）

##### 安装CCAA


	yum -y install wget unzip curl
	wget https://github.com/helloxz/ccaa/archive/master.zip
	unzip master.zip && cd ccaa-master && sh ccaa.sh

一步一步执行就可以了，中间输入的密码要牢记


![](https://ws1.sinaimg.cn/large/9f723435ly1fw0ldi7tcmj20i70gdq42.jpg)

##### 打开AriaNg界面
-在浏览器输入http://IP：6080/
![](https://ws1.sinaimg.cn/large/9f723435ly1fw0lf6rhj4j213t0de3zh.jpg)

这里输入完，安装的时候设置的秘钥，刷新界面就可以了。