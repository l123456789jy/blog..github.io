---
layout:     post
title:      "CentOS/UbuntuVPS上安装htop并查看CPU使用情况"
subtitle:   ""
date:       2018-10-08 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - VPS
---



### 安装 htop 命令
- CentOS系统
 第一种方案：
	  yum -y install epel-release

	  yum -y update

	  yum -y install htop

第二种方案：

	 yum -y install ncurses-devel wget

	wget http://hisham.hm/htop/releases/2.0.2/htop-2.0.2.tar.gz

	tar xvfvz htop-2.0.2.tar.gz

	cd htop-2.0.2

	./configure --disable-unicode && make && make install

安装完成后，输入 htop 查看是否安装成功

- Debian / Ubuntu：

		apt install -y htop

输入 htop 回车之后，就可以看到 htop 页面

![](https://ws1.sinaimg.cn/large/9f723435ly1fw0ka7gv5bj20y40hgtax.jpg)