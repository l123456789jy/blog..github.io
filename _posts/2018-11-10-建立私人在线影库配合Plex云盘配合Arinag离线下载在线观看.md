---
layout:     post
title:      "建立私人在线影库配合Plex云盘配合Arinag离线下载在线观看"
subtitle:   ""
date:       2018-11-10 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags: vps
    -
---


#### 建立私人在线影库配合Plex云盘配合Arinag离线下载在线观看

参考（https://www.moerats.com/archives/464/）
- Plex的全称为Plex Media Server，主要功能是存储+索引+转码+在线播放，它不仅仅是简单的存储，它还能分析影片的信息从而从IMDB等数据库补全影片介绍等信息，并且进行索引以方便搜索，要知道影片的格式有很多种，很多情况下播放设备无法兼容，所以它还能实时转码，播放渠道从网页到全平台的客户端应有尽有。当然，Plex除了能存片，还能存图存歌等等


- 效果图

![](http://ww1.sinaimg.cn/large/9f723435ly1fx32ale8cxj21050foqnh.jpg)

- 我的是cenos6 64位的执行下面命令

		yum -y update && yum -y install wget
		wget https://downloads.plex.tv/plex-media-server/1.12.1.4885-1046ba85f/plexmediaserver-1.12.1.4885-1046ba85f.x86_64.rpm
		yum install plexmediaserver*.rpm



- 安装完毕之后启动plex

 service plexmediaserver start
 查看下启动完毕状态
  service plexmediaserver status

确认是运行状态之后，别着急打开


- 我们需要下载个putty进行隧道代理，要不然plex是会找不服务器的

![](http://ww1.sinaimg.cn/large/9f723435ly1fx32f2j91ej20e60d5wfw.jpg)

在这里我们先输入我们自己安装的那个远程服务其的Ip和端口，输入完毕之后配置隧道，点击opens在打开的窗口输入我们的远程服务用户名和密码，之后别关闭窗口执行下面方法

![](http://ww1.sinaimg.cn/large/9f723435ly1fx32gu42vnj20du0ci75f.jpg)
按照我截图的方式配置完毕之后，在你本机就是自己笔记本上浏览器上输入这个地址
http://127.0.0.1:8888/web/index.html#

![](http://ww1.sinaimg.cn/large/9f723435ly1fx32jezzodj20sl0gx7fi.jpg)


![](http://ww1.sinaimg.cn/large/9f723435ly1fx32kq6vrvj20sj0gb13h.jpg)

一步执行就可以了，执行完毕之后，我们就可以直接输入远程地址（http://IP:32400/） 就可以访问了使用了，


