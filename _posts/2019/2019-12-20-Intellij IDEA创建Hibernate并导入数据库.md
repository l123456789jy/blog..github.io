---
layout:     post
title:      "Intellij IDEA创建Hibernate并导入数据库"
subtitle:   ""
date:       2019-12-20 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - java
---


- 第一种方式我们创建项目的时候要记得勾选 hibernate
![image.png](https://upload-images.jianshu.io/upload_images/1205414-98f3d4e175fbbfd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 如果我们创建项目的时候没有勾选也可以添加

![image.png](https://upload-images.jianshu.io/upload_images/1205414-8c9d1503e6d101e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在选择添加hibernate即可




- 使用hibernate我们需要先连接好本地数据库

![image.png](https://upload-images.jianshu.io/upload_images/1205414-46763cf3701e369e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 这里的配置就按照默认配置就行，输入密码

![image.png](https://upload-images.jianshu.io/upload_images/1205414-a490566d2323b02e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果连接失败，请设置 serverTimezone=UTC 在Advanced这个选项


- 在我们配置好就可以看到我们建立的数据库表结构了

![image.png](https://upload-images.jianshu.io/upload_images/1205414-1d4fb4d44936ab43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 接下来就可以配置了hibernate向这个user表自动插入数据了
![image.png](https://upload-images.jianshu.io/upload_images/1205414-d31eed7ec19c179a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

项目左下角找到如图，然后在下面右键

![image.png](https://upload-images.jianshu.io/upload_images/1205414-a5877f064d83ec1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/1205414-9ff143636bc5679a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面选择配置好的数据库，就可以看到当前的表了，然后勾选需要生成的表就可以了，这里使用的是注解方式生成

![image.png](https://upload-images.jianshu.io/upload_images/1205414-1a51c28293c37ece.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/1205414-259099b34598fa80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上面就是生成的配置文件，简单需要自己改下就可以了，我们需要在servise成调用记得lib要放到WEB-INF下

![image.png](https://upload-images.jianshu.io/upload_images/1205414-0f93d7827664cd5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/1205414-7a19ab8c9133e40c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)











