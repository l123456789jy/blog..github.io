---
layout:     post
title:      "Windows添加digDNS测试工具"
subtitle:   ""
date:       2018-11-02 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    -
---
#### 为Windows添加dig DNS工具
- 下载地址：https://www.isc.org/downloads/file/bind-9-12-3/?version=win-64-bit
这个版本可能你看到时候已经不是最新了，请到官网https://www.isc.org下载最新版本，不在乎可以直接使用当前连接下载

- 突然发现有写网站死活打不开，发给别人就可以打开，最后感觉是DNS问题，一看我的DNS是自动获取，换上腾讯的DNS就可以了，腾讯DNS地址文档：https://www.dnspod.cn/Products/Public.DNS

- 如果我们想要在windows测试每个DNS解析速度，选出当前地区的最好的DNS怎么办呢？这里就可以使用dig工具

- 我们下载下载之后一定要右键以管理员身份运行要不然安装失败
![](https://ws1.sinaimg.cn/large/9f723435ly1fwtj4qx4j0j208g0evaa0.jpg)


- 参照我的截图勾选，需要把BIND的目录添加到PATH中以方便命令行中调用dig等工具。

- 环境变量添加完毕之后我们在控制台输入dig
![](https://ws1.sinaimg.cn/large/9f723435ly1fwtj7sb5n1j20ft03o743.jpg)
这样就可以了配置正确了

- 比如我要测试腾讯使用114DNS解析时间，就输入 dig @114.114.114.114 www.qq.com

![](https://ws1.sinaimg.cn/large/9f723435ly1fwtj9yl8i2j20gq0awq2z.jpg)

这里就可以看到他的解析时间是23ms，ip等等信息

不需要安装的在线GUI界面：https://www.diggui.com/

