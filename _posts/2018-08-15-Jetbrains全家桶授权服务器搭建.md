---
layout:     post
title:      "Jetbrains全家桶授权服务器搭建"
subtitle:   ""
date:       2018-08-15 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - IDEA
---


- 用于破解IntelliJ IDEA、PHPStrom、PyCharm、WebStrom等IDE产品，还是有能力去支持正版

- 搭建环境：CentOS 6 64位

- 软件下载地址：[点我下载](https://valleyecho-1252960615.cosgz.myqcloud.com/IntelliJIDEALicenseServer%280.0.0.0_1017%29.zip "点我下载") 软件作者是 Lanyu，更多的用法请去他的博客看
- 压缩包解压有多个环境运行版本，我们选择linux的
![](http://ww1.sinaimg.cn/large/9f723435ly1fuaex4ivajj20kj0d80v2.jpg)

- 然后将这个文件拖到我们自己的VPS服务器上去,后面的文件idea是我自己重命名之后的

- chmod +x ./idea #授予执行权限
- ./idea #运行
![](http://ww1.sinaimg.cn/large/9f723435ly1fuaeyqztgyj20k904jq33.jpg)
这样就代表运行成功
- 但是上面的我们必须要在后台一直执行，还需要能外网访问，这个是127.0.0.1地址

- screen -dmS idea ./idea #让IdeaServer在后台运行
- 然后执行 top命令查看正在后台跑的程序
![](http://ww1.sinaimg.cn/large/9f723435ly1fuaf1fm39mj20li0egjsk.jpg)

- 添加开机启动
- vim /etc/rc.local，添加以下代码
    cd /root/
    screen -dmS idea./idea

- 通过Nginx反向代理http://127.0.0.1:1017，我们就能在远程实现激活

