---
layout:     post
title:      "BuildConfig使用"
subtitle:   ""
date:       2016-07-04 10:16:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android

---








##在我们每次发版的时候总需要自己写个变量来手动的改，是否是debug模式，来更改某些配置，现在我们使用系统给我们提供的BuildConfig这个类，就可以不用管了，他会再你打包的时候自动改变数值


```java
public final class BuildConfig {
  public static final boolean DEBUG = Boolean.parseBoolean("true");
  public static final String APPLICATION_ID = "com.xxxxx";
  public static final String BUILD_TYPE = "debug";
  public static final String FLAVOR = "_main";
  public static final int VERSION_CODE = 24;
  public static final String VERSION_NAME = "3.9.2";
}


```


##这个BuildConfig会生成在你as的

    app\build\generated\source\buildConfig\_main\debug\your_pagename\BuildConfig.java

目录下面我们不能去手动的修改该配置，他在as编译的时候会自动的修改，所以我们在项目中就可以使用

```java
if (BuildConfig.DEBUG){
xxxxxx
    }

```

这样来更改配置了，是不是方便了很多。
