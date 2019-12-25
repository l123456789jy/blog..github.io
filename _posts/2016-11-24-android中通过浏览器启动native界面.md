---
layout:     post
title:      "android中通过浏览器启动native界面"
subtitle:   ""
date:       2016-11-24 11:30:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android

---









##1.首先我们看下效果，我在本地搭建了一个Tomact服务，然后手机连接打开网页


![device-2016-11-24-104839.png](http://upload-images.jianshu.io/upload_images/1205414-055a0884b8775c86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





##2，然后我们点击此连接打开native界面

![device-2016-11-24-105035.png](http://upload-images.jianshu.io/upload_images/1205414-b0bd67df6ba2248c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##3.我们看下代码

```java

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
        "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<!-- saved from url=(0032)http://localhost:8080/jsandroid/ -->
<html xmlns="http://www.w3.org/1999/xhtml">
<html>
<body>
<h1>路由跳转</h1>
<html>
<body><a href="testapp://haha/open?name=lisi&age=30&from=ucbroswer" font-size="45">启动apk</a></body>
</html>


```
最主要就是我们的`testapp://haha/open?name=lisi&age=30&from=ucbroswer`  这个代表什么呢，这个就是我们定义好的约束，通过给activity制定约束条件就可以打开我们的activity

我们看下activity的配置

```java

<activity android:name=".RouteTestActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <!--下面所设置的质需要和html端对调-->
                <!--在data里设置了 scheme和host，则该Activity可以接收和处理类似于 "sharetest://data/XXX"的链接-->
                <data
                    android:host="haha"
                    android:pathPrefix="/open"
                    android:scheme="testapp" />
            </intent-filter>

        </activity>

```

其实最重要的就是我们使用了intent-filter，就可以实现这种效果了


##4.
另外webview直接加载也是可以的

代码地址：https://github.com/l123456789jy/AndroidTestDemo/blob/master/Demo/src/main/java/com/example/administrator/myapplication/RouteTestActivity.java





