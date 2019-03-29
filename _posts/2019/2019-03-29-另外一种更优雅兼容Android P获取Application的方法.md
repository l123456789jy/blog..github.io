---
layout:     post
title:      "另外一种更优雅兼容Android P获取Application的方法"
subtitle:   ""
date:       2019-03-29 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog: svn
tags:
    -
---

- 我们知道我们要想获取Application的方法很多，有没有一种更优雅方式呢？我们的Android系统中提供了获取Application的方法但是都是hiden，具体代码参看[http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/app/AppGlobals.java#31](http://androidxref.com/9.0.0_r3/xref/frameworks/base/core/java/android/app/AppGlobals.java#31)


- 那么我们有没有什么优雅的方法去调用hiden的API呢？

   有这里我们使用的是类加载器的委托机制，具体怎么做呢。



-

```java
package android.app;


public class ActivityThread {

    public static Application currentApplication() {
        return null;
    }
}

```

![image.png](https://upload-images.jianshu.io/upload_images/1205414-a0daffd33f758410.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



```java
try {
      //兼容android P，直接调用@hide注解的方法来获取application对象
      Application app = ActivityThread.currentApplication();
      Log.e("MainActivity", app.getPackageName());
    } catch(Exception e) {
      e.printStackTrace();
    }
    try {
      //兼容android P，直接调用@hide注解的方法来获取application对象
      Application app = AppGlobals.getInitialApplication();
      Log.e("MainActivity2", app.getPackageName());
    } catch (Exception e) {
      e.printStackTrace();
    }


这里需要注意，我们在本地定义的两个ActivityThread和AppGlobals要以android.app包名来命名，这样就可以欺骗编辑器，然后根据类加载器的委托机制，他会直接加载系统的ActivityThread和AppGlobals因此就可以直接获取application了


