---
layout:     post
title:      "androidsdk tools工具使用"
subtitle:   ""
date:       2018-09-10 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 
---

##### 这里我使用的手机环境是root,debug等于true,也就是说我手机里面额所有应用都可以调试

##### `androidsdk tools`目录位置在自己的`androidsdk tools`目录下面
![](http://ww1.sinaimg.cn/large/9f723435ly1fv5b732htxj20qg0hsq4u.jpg)


##### 这里我们介绍常用的工具

- `ddms.bat`我们双击启动，如果报端口被占用，先拔掉usb，在连接就可以了

![](http://ww1.sinaimg.cn/large/9f723435ly1fv5bae63g2j21300jojt0.jpg)
这里就可以看到我们所有可调式的进程，点击其中需要调试的进程
![](http://ww1.sinaimg.cn/large/9f723435ly1fv5bbtcg8ej20vs0e60u8.jpg)
点击ok之后，我们操作当前需要调试的界面，结束之后手动在点击一次开始的位置就可以了.
![](http://ww1.sinaimg.cn/large/9f723435ly1fv5beaaawnj20yr0m83zy.jpg)
一次找寻需要的方法就可以了，具体详细使用谷歌搜索

- `hierarchyviewer.bat` 双击打开
![](http://ww1.sinaimg.cn/large/9f723435ly1fv5bfw2nx5j20zl0j83z8.jpg)
双击需要调试的进程
![](http://ww1.sinaimg.cn/large/9f723435ly1fv5bj2w9vej20yw0li40z.jpg)
这里就可以看到当前调试界面的所有布局，和绘制时间，详细使用方法谷歌


- `uiautomatorviewer.bat`显示当前界面的布局规则和id==，一般做逆向，用的多

![](http://ww1.sinaimg.cn/large/9f723435ly1fv5bmt4ol6j212z0mkwgl.jpg)

这里我门可以清楚看到微信当前界面的布局和资源id，有了这些工具基本分析一个应用会很快得到自己想要的信息