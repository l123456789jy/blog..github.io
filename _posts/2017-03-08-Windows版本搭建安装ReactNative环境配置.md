---
layout:     post
title:      "Windows版本搭建安装React Native环境配置"
subtitle:   ""
date:       2017-03-08 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
---



- 最近需要使用Rn，记录下搭建步奏


1.下载node 安装包，傻瓜式安装，安装完毕之后，在命令还查看是否安装完成

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-653412d377e32d8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果出现上面提示就是安装成功


2.犹豫我是做安卓的所以  安装Java，安装SDK，这两个环境已经安装完毕了。所以直接下载安装安装git  GUI图形化工具，然后在自己的文件夹下建立一个文件夹，选择git init here
初始化Git，在选择 git gui 显示出GUI画面，输入git clone https://github.com/facebook/react-native.git，等待下载


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-144fca3dfd4e837b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



下载完毕


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-1d8eb7bdfb32db80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


3.进入刚刚目录下的react-native目录下的react-native-cli目录，输入npm install -g
shit+右键在此处打开命令行，输入此命令

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-bd70df54d061598e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装好之后，可以命令行下就有react-native命令了

4.开始创建Rn项目

进入你希望创建项目的目录后，输入react-native init （xxx）项目名字，等待一段时间（较慢）


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-2b64a16077f5cd8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这一步如果太慢，可以自己去下载YARN  https://yarnpkg.com/zh-Hans/docs/install#windows-tab 进行安装，应为他会先去下载YARN

进度看这里：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-d2ab1e2c045397e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###建议设置 npm 镜像以加速后面的过程（或使用科学上网工具）。
            npm config set registry https://registry.npm.taobao.org --global
            npm config set disturl https://npm.taobao.org/dist --global


5.细心等待

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-d1b2350081c9abe4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这样就成功啦！


6.开启本地node服务
       在命令行中进入项目目录，输入react-native start，等待一段时间：



![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-ba41148a05f51964.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后在浏览器输入下面地址：http://localhost:8081/index.android.bundle?platform=android


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-b17ac77f51fc129c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7.这里如果直接去 react-native run-android会有问题的，因为会有各种依赖需要下载，这里最好打开Androidstudio用AS来下载各种依赖

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-8c40df00596e9968.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

正常导入AS的效果，然后在自己的工程目录下运行react-native run-android，或者直接在AS中运行。


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-c44f78b85b962cb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


最后看到下面界面就可以了。


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-c0fc1de85aa48081.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果不行，摇下设置下ip就行啦！