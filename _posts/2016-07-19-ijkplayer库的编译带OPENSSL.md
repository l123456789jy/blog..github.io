---
layout:     post
title:      "ijkplayer库的编译带OPENSSL"
subtitle:   ""
date:       2016-07-19 13:37:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - Https
    - ijkplayer
---








##由于项目中要进行加密播放HLS格式的视频，所以就打算用B站的库了，由于使用的是Windows平台，不想麻烦再去编译库，就直接找了编译好直接用，可是悲剧的是，大部分都是没有编译openssl，不支持HTTPS，坑死我，所以只好自己动手丰衣足食。


- 我在windows上编译了下各种错，所以就直接搭建哥虚拟机在乌班图搞了


- 在windows行如何装虚拟机就不说了。
虚拟机下这些需要注意，如果你想安装乌班图64位，那么就要开启电脑的虚拟映射，在Bisso里面。具体谷歌。



##1. 确保你的机器上已经安装了git和yasm，如果没有请在终端执行以下命令：
    sudo apt-get update
    sudo apt-get install git
    sudo apt-get install yasm



##2.编译的具体步骤


     直接拉取源代码到本地
    git clone https://github.com/Bilibili/ijkplayer.git ijkplayer-android
    cd ijkplayer-android

     检查更新代码  这一步是切换到0.5.1版本，具体的你可以看自己而定
    git checkout -B latest k0.5.1

     初始化，会把ffmpeg的代码拉取到本地等等操作
    ./init-android.sh
    cd android/contrib

    ./compile-ffmpeg.sh clean
     编译ffmpeg软解码库，这个过程会生成各种架构的ffmpeg 这个过程比较耗时
    ./compile-ffmpeg.sh all

    我们退到上一级目录
    cd ..
     会生成各种版本的so文件
    ./compile-ijk.sh all

最终我们会看到

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-b99b4e495982b700.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#需要注意的：

   NDK要用10以上的
    目录一定不要有空格。
    网上大多数说要配置ANDROIDSDK的环境，如果你不在乌班图下开发完全没必要，编译完成之后，直接把项目拷贝到windows下编译就行了。


##接下来我们来编译openssl

##1.init openssl

    $ cd ..    进入到ijkplayer的目下
    $ ./init-android-openssl.sh   去远程仓库拉取openssl的远程代码
    
    
    
##2.compile openssl

    $ cd android/contrib
    $ ./compile-openssl.sh clean
    $ ./compile-openssl.sh all



##经过以上步骤已经编译好openssl了，然后我们执行一下方法
      ./compile-ffmpeg.sh clean
         编译ffmpeg软解码库，这个过程会生成各种架构的ffmpeg 这个过程比较耗时
        ./compile-ffmpeg.sh all
 
 
 ##如果你看到so库文件大于3兆恭喜你编译成功
 ##如果so库大小为3兆以内那就是opeenssl没有编译成功


##在乌班图配置环境我们可以这样快速配置：




送上地址：https://github.com/l123456789jy/ijkplayer
