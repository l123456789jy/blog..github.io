---
layout:     post
title:      "正确为AndroidStudio配置ss代理"
subtitle:   ""
date:       2018-11-20 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog: 代理
tags:
    -
---

#### 正确为AndroidStudio配置ss代理

- 打开Android Studio，进入Preferences，搜索proxy关键字，进入proxy设置页面。
- 打开本地代理软件SS,等其他socks代理软件

![](https://ws1.sinaimg.cn/large/9f723435ly1fxele5ehvmj20q90j20u7.jpg)


- 但是这样只能下载sdk,这样配置之后有没有发现我们使用Gradle下载某些依赖的时候还是下载不下来，不对啊，我们吧㐊配置代理，那是应为gradle不支持socks代理，这里我们可以将socks转为http代理，或者给jvm设置代理


- 在AS中我们可以直接给JVM设置代理

		org.gradle.jvmargs=-DsocksProxyHost=127.0.0.1 -DsocksProxyPort=1080


这里的端口要根据你自己代理的端口变化，一般都是1080

![](https://ws1.sinaimg.cn/large/9f723435ly1fxelingaj4j20qc0b70tk.jpg)

在我们的跟目录的 gradle.properties  加上上面配置就可以下载了。







