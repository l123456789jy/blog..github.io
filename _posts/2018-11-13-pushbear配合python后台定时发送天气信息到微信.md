---
layout:     post
title:      "pushbear配合python后台定时发送天气信息到微信"
subtitle:   ""
date:       2018-11-13 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:
tags:
    -
---

#### pushbear配合python后台定时发送天气信息到微信

- 一直想要在早上起床能看到当天天气信息
- 而且能发送到微信上这里就用（pushbear）
- api接口使用https://www.heweather.com/

#### 代码没啥就是获取天气信息然后解析调用pushbear接口发送到微信

代码地址：https://github.com/l123456789jy/weather


#### 脚本执行的地址需要是全路径地址

/usr/local/bin/python3.6 /jiaoben/weather.py

![](https://ws1.sinaimg.cn/large/9f723435ly1fx6j7w8ymjj20xo04u0sq.jpg)

后台配置定时任务就可以了，注意服务器时区要调整到中国要不然就不对了

![](https://ws1.sinaimg.cn/large/9f723435ly1fx6iwrkwi9j20et0bfglx.jpg)





