---
layout:     post
title:      "ReactNative打包"
subtitle:   ""
date:       2017-03-15 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
---



- 1.我是做原声开发，所以当然直接在Androidstudio上打包了,先看下打包成功之后的工程目录结构

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-32c41d423ba65e40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 2.在我们Rn打包中最主要的就是需要index.android.bundle这个资源包，这个是Rn的所有源码。
- 3.我们在AS选中下面所示

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-4da856b95909c641.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


会在指定目录下生成正式签名，会在发版的时候使用。

- 4.所以这里我们主要是打包bundle这个资源包，我们进入到自己的Rn工程的根目录，运行下面的指令：

react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-98387e5a5e1e280f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行成功之后就会在Android工程目录生成index.android.bundle这个资源包。

- 5，然后我们执行打包

![](http://upload-images.jianshu.io/upload_images/1205414-a42ec4fc4ee9bfa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打完之后的安装包，就可以直接安装使用了，不再需要连接nodes了。
附上体验的APK: https://github.com/l123456789jy/RnProject/blob/master/apk/app-debug.apk