---
layout:     post
title:      "Android动态加载dex技术初步了解"
subtitle:   ""
date:       2017-09-20 15:30:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Dex
---
##1.概述

 - 1.1   Android使用Dalvik虚拟机加载可执行程序，所以不能直接加载基于class的jar，而是需要将class转化为dex字节码，从而执行代码。优化后的字节码文件可以存在一个*.jar中，只要其内部存放的是*.dex即可使用，这里我们直接使用AS工程
直接打包出APK然后解压去除dex文件即可

- 1.2   Android支持动态加载的两种方式是：DexClassLoader和PathClassLoader，DexClassLoader可加载jar/apk/dex，且支持从SD卡加载；PathClassLoader只支持加载已经安装到 Android 系统中的 apk 文件

- 1.3 接下来我们使用DexClassLoader实现加载sd卡的dex文件并且调用内部的方法



##2.实践
  - 2.1 首先我们定义个IShowToast接口，这个其实要不要都可以



```java
public interface IShowToast {

  public int showToast(android.app.Application context);
}

```


- 2.2 在定义实践类


```java
public class ShowToastImpl implements IShowToast {

  @Override
  public int showToast(android.app.Application context) {
    Toast.makeText(context, "我来自另一个dex文件", Toast.LENGTH_LONG).show();
    return 100;
  }

  public void test() {
    Log.e("\"我来自另一个dex文件\"","我来自另一个dex文件");
  }
}


```


- 2.3 跑当前的Android工程生成dex文件，将生成的dex文件拷贝到sd卡下


- 2.4 另开启一个新工程测试代码如下

```java
public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    try {
      File dexOutputDir = getDir("dex1", 0);
      String dexPath = Environment.getExternalStorageDirectory().toString() + File.separator + "a";
      File file = new File(dexPath);
      Log.e("MainActivity",file.length()+"");
      DexClassLoader loader = new DexClassLoader(dexPath,
          dexOutputDir.getAbsolutePath(),
          null, getClassLoader());
      Class clz = loader.loadClass("com.Company.Demo.ShowToastImpl");
      Method m1 = clz.getDeclaredMethod("test");
      Method showToast = clz
          .getDeclaredMethod("showToast", new Class[]{getApplication().getClass()});
      m1.invoke(clz.newInstance());
      showToast.invoke(clz.newInstance(),getApplication());
    } catch (Exception e) {
      Log.e("MainActivity", "error happened", e);
    }
  }
}



其实这里就是通过DexClassLoader得到sd卡的dex的ShowToastImpl对象然后通过反射即可调用器内部的方法

```


![](http://ww1.sinaimg.cn/large/9f723435ly1fjq1hxaujlj21401z4jsn.jpg)
然后就可以愉快的玩耍了

  此处需要注意DexClassLoader的四个参数：
               参数1 dexPath：待加载的dex文件路径，如果是外存路径，一定要加上读外存文件的权限（<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/> ），否则会报与上面一样的错误，这点参考文章2中说这个权限可有可无是错误的。(更正下：Android4.4 KitKat及以后的版本需要此权限，之前的版本不需要权限)

               参数2 optimizedDirectory：解压后的dex存放位置，此位置一定要是可读写且仅该应用可读写（安全性考虑），所以只能放在data/data下。本文getDir("dex1", 0)会在/data/data/**package/下创建一个名叫”app_dex1“的文件夹，其内存放的文件是自动生成a.dex；如果不满足条件，Android会报的错误为：

              java.lang.IllegalArgumentException: optimizedDirectory not readable/writable: /storage/sdcard0

              java.lang.IllegalArgumentException: Optimized data directory /storage/sdcard0 is not owned by the current user. Shared storage cannot protect your application from code injection attacks.

               参数3 libraryPath：指向包含本地库(so)的文件夹路径，可以设为null

               参数4 parent：父级类加载器，一般可以通过Context.getClassLoader获取到，也可以通过ClassLoader.getSystemClassLoader()取到。