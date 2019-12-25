---
layout:     post
title:      "reretrofit2遇到的坑"
subtitle:   ""
date:       2016-07-03 14:16:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - reretrofit2
---





#reretrofit2遇到的坑
    Caused by: java.lang.IllegalArgumentException: baseUrl must end in /:
如果报这样的错误，首先要检查下你的连接是否含有&含有这个目前我这无法正常请求




#reretrofit2,直接请求一个uri的方法



```java
	
public interface IUserBiz {
    @GET
    Call<CiaddifiCationBean> getUsers(@Url String url);
}

```



```java
  //https://api.douban.com/v2/movie/top250?start=0&count=10
        String profile_url =" https://api.douban" +
                ".com/v2/movie/top250?start=0&count=10/";
        //String profile_url =" http://gank.io/api/data/Android/10/1/";
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(profile_url)
                .addConverterFactory(GsonConverterFactory.create())
                .build();
        IUserBiz userBiz = retrofit.create(IUserBiz.class);
        userBiz.getUsers("");

```
