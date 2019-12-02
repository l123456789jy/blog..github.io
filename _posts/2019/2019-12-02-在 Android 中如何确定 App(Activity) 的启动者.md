---
layout:     post
title:      "在 Android 中如何确定 App(Activity) 的启动者"
subtitle:   ""
date:       2019-12-02 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:
tags:
    - Android
---


- 有时候我们需要定位某个APP无缘无故在后台开启，被谁打开调起的我们需要知道，经过测试如下方法可以定位


- 首先我们把测试机连接上AS然后过滤池标签“ActivityManager: START”

![微信截图_20191202103905.png](http://ww1.sinaimg.cn/mw690/9f723435ly1g9i6kjpyljj212v064aau.jpg)


- 就可以看到每次启动的Activity的日志，包括启动的完整包名路径，和被启动的UID,这里我们通过测试在UC浏览器中吊起APP，我们看到大码的那条信息就是我们需要追踪的信息，后面有from uid 10223这个id，也就是说是被10223这个id启动于是我们过滤这个id

- ps | grep 10223
会发现没有，由于是测试我们知道启动者是UC，我们看看UC的完整名字是多少

- ps | grep com.UCMobile


![微信截图_20191202104009.png](http://ww1.sinaimg.cn/mw690/9f723435ly1g9i6oass89j20p104tdfz.jpg)

注意到没有，UC前面有个统一标识：u0_a223他和10223，是不是后面223是一样的，我们看看这个代表什么意思

- u0_a223

u0 默认的手机第一个用户（可以通过设置里面的多用户新增和切换）
a 代表app
223 代表着第223个应用

也就是说UC在我这个测试机中是滴223个应用，所以以后就可根据UID后面几位来定义追踪的被启动应用了


为了验证是不是这样，我们测试下，我们通过点击“launcher”来打开，看看是不是指向launcher

![微信截图_20191202105231.png](http://ww1.sinaimg.cn/mw690/9f723435ly1g9i6ulactwj211a0160sp.jpg)

可以看到被启动的后几位id是082，那我们看看“launcher”

![微信截图_20191202105329.png](http://ww1.sinaimg.cn/mw690/9f723435ly1g9i6vkbfqjj20lt010q2q.jpg)

“u0_a82” 可以看到launcher”是“82”所以基本可以确定这种方法是可以确定被启动的应用名字是谁的。




参考此文章：

https://droidyue.com/blog/2019/12/01/android-uid-process-name/