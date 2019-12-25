---
layout:     post
title:      "不用去配置gradle而且想用哪个版本用哪个版本gradle来编译你的项目"
subtitle:   ""
date:       2019-04-10 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog: gradle
tags:
    -
---
- 我们有时候AS编译出错了，我们需要获取详细的报错信息，需要在控制台执行获取更多的日志信息，这个时候如果我们没有配置gradle环境还的去配置是不是很麻烦，有一种方法不用去配置，而且想用哪个版本用哪个版本gradle



- 我们都知道gradle安装完毕之后有一个人目录

D:\android\gradle\bin\gradle.bat   app:assemblexiaomiDebug --stacktrace

我们只需要在我们项目的根目录执行以上命令格式就可以，就是告诉gradle你要使用的gradle.bat的目录即可，是不是方便了许多