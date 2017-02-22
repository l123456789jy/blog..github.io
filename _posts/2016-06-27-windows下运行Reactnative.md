---
layout:     post
title:      "windows下运行Reactnative"
subtitle:   ""
date:       2016-06-27 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
---




##windows下运行Reactnative经历的坑
-  npm install -g react-native-cli

 当我们执行rn工程初始化的时候这个一步可能会很长时间，多试几次。




-  react-native init AwesomeProject

  创建工程的时候 AwesomProject下的anroid下的local.properties文件是没有的，需要我们自己去拷贝一个里面指定的是你本地的android sdk的安装目录



- react-native run-android

 有时候我们设定了android sdk 的路径但是还是报错找不到，我们就需要手动的去制定了 set ANDROID_HOME=D:\AndroidSdk  然后再重新跑


- rn服务开启之后，直接用as把android项目跑起来，其中下载依赖的时候时间较长，要等等！gradle的版本直接自己改成本地有的版本就行！

