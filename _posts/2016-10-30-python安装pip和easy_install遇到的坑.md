---
layout:     post
title:      "python安装pip和easy_install遇到的坑"
subtitle:   ""
date:       2016-10-30 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Python
---






1.这两个是做什么的呢？
  他是我们下载依赖包所需要的，没有他们我们无法下载相应的三方库

2.首相确保你安装的python
3.然后我们进入到这个网站 https://pypi.python.org/pypi/setuptools

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-d6c9844afed09852.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-b9d8c38f4e41ee66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击之后把里面的python复制下来，新建一个文件和他一样的名字

4.然后进入到当前你保存这个文件的目下下，打开CMD
输入 python  ez_setup.py   
等他下载完毕之后在你python的安装目录下有

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-085376acaff5de02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这样就可以啦，然后我们把环境变量配置进去就可以啦！

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-7166c9712ac68e81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

加上这个就行了，pip也是同理
