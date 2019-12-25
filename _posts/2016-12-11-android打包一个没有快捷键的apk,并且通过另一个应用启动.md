---
layout:     post
title:      "android打包一个没有快捷键的apk,并且通过另一个应用启动"
subtitle:   ""
date:       2016-12-11 14:44:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android

---







1.有时候我们的需求是每个功能可能类似一个插件那样分开，需要我们分别不同开发

2.首先我们如何安装apk之后不显示Lanch呢？
  ```java
    <activity
            android:name=".app.Books.Books"
            android:configChanges="orientation|keyboardHidden|screenSize|smallestScreenSize"
            android:launchMode="singleTask"
            android:theme="@style/SampleAppsTheme">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <!--   <category android:name="android.intent.category.LAUNCHER" />-->
            </intent-filter>


        </activity>

```
我们只需要把`<category android:name="android.intent.category.LAUNCHER" />`注掉就可以啦，记住这里需要注意，我们想要调用的那个界面必须要加上`  <action android:name="android.intent.action.MAIN" />`要不然会报错。

3.接下来我们就直接这样方式启动就行啦！
```java
 Intent intent = new Intent(Intent.ACTION_MAIN);
                intent.addCategory(Intent.CATEGORY_LAUNCHER);
                ComponentName cn = new ComponentName("com.vuforia.samples.Books", "com.vuforia.samples.Books.app.Books.Books");
                intent.setComponent(cn);
                startActivity(intent);

```
第一个是我们需要打开应用的包名，第二个是我们打开的界面类名，这只是其中一种方式，方式很多

4.如果我们需要把值传递过来只需要这样就可以
```java
Intent intent = new Intent();
intent.putExtra("test","test");
setResult(10,intent);
finish();

```
