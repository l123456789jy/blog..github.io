---
layout:     post
title:      "Fiddler抓包出现tunnel to 443"
subtitle:   ""
date:       2017-05-16 15:30:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Fiddler
---
1.
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-0379fa5cb7a63f90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.今天使用Fiddler抓包的时候出现了tunnel to 443的错误，导致抓包https的接口数据抓不到，造成这个原因就是因为，这个证书不是购买的那种信任证书，需要手机端安装FiddlerRoot.cer证书

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-2dc0b128bbb18813.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.首先确保打开Fiddler,手机WIF这只好自己代理，确保在同一个网络，在手机端浏览器输入下面的网址：http://你挂代理的ip:8888/FiddlerRoot.cer

4.安装成功之后我们就能看到，会提示有未知的网络监控，我查看详情：


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-82ce0d98d15d70fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个就是我们安装的证书，然后就可以愉快开始抓包了。