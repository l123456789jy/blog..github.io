---
layout:     post
title:      "OkHttp结合Stetho调试"
subtitle:   ""
date:       2017-04-20 15:32:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
---

- Stetho地址：http://facebook.github.io/stetho/
- 我使用的OkHttp2.7的版本，所以和3.0导入的依赖不一样
- 如果是OkHttp3.0导入一下库：

         dependencies {
            compile 'com.facebook.stetho:stetho-okhttp3:1.5.0'
          }


-  如果是OkHttp3.0以下导入下库：

          dependencies {
            compile 'com.facebook.stetho:stetho-okhttp:1.5.0'
          }

- 导入完成之后我们在我们的OkHttp中这种写：

```java
private static OkHttpClientManager mInstance;
    public static OkHttpClient mOkHttpClient;
    private static Handler mDelivery;
    private static final String TAG = "OkHttpClientManager";
    private static Handler mHandler=new Handler(Looper.getMainLooper());

    private OkHttpClientManager() {
        //增加http请求的缓存
        int cacheSize = 10 * 1024 * 1024; // 10 MiB
        Cache cache = new Cache(new File(MyApplication.getInstance().getCacheDir().getPath()),cacheSize);
        mOkHttpClient = new OkHttpClient();
        //设置超时
        mOkHttpClient.setWriteTimeout(20, TimeUnit.SECONDS);
        mOkHttpClient.setReadTimeout(20, TimeUnit.SECONDS);
        //错误重连
        mOkHttpClient.setRetryOnConnectionFailure(true);
        //cookie enabled
        mOkHttpClient.setCookieHandler(new CookieManager(null, CookiePolicy.ACCEPT_ORIGINAL_SERVER));
        //暂时关闭请求缓存导致社区评论刷新不及时
        //mOkHttpClient.setCache(cache);
        //mOkHttpClient.networkInterceptors().add(REWRITE_CACHE_CONTROL_INTERCEPTOR);
        mDelivery = new Handler(Looper.getMainLooper());
        mOkHttpClient.networkInterceptors().add(new StethoInterceptor());



    }

```

- 主要是加上了`mOkHttpClient.networkInterceptors().add(new StethoInterceptor());`，添加了拦截器，添加完成之后，先打开Chrome浏览器然后在浏览器输入`chrome://inspect`

![image](https://wx2.sinaimg.cn/large/9f723435ly1fetclynz9nj20ez093mxu.jpg)

- 点击  inspect 就会打开一个新的界面如下
在此界面可以看到布局结构
![image](https://ws2.sinaimg.cn/large/9f723435ly1fetcorg92nj210w0hs0wi.jpg)


在此界面可以看到我们请求的地址和时间，这点可以很方便我们测试接口的延迟和调试
![image](https://wx2.sinaimg.cn/large/9f723435ly1fetcq2ekq6j210p097gnf.jpg)


最重要一点我们的数据库一般都是存放到data目录下面，如果要查看数据库就需要Root，有了他我们就不需要Root了
![image](https://wx1.sinaimg.cn/large/9f723435ly1fetd1vh820j210s0fymyo.jpg)

有没有，有了他我们调试数据库再也不麻烦了。








