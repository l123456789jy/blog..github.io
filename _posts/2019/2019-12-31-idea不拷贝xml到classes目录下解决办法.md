---
layout:     post
title:      "idea不拷贝xml到classes目录下解决办法"
subtitle:   ""
date:       2019-12-31 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - idea
---





- 之前写好的项目突然运行不了，印象中好像点了什么东西


- 后来发现原来maver中引用了`org.apache.maven.plugins`


- 这样打包默认不会打src下的xml配置文件`hibernate.cfg.xml`了，解决办法将配置文件放到`resources`目录下面就可以了，我们可以看看打包后结构

![微信截图_20191231093807.png](http://ww1.sinaimg.cn/large/9f723435ly1gafnod3k55j209w08tt8o.jpg)


- 可以看到直接拷贝到classes下面，所以也不影响默认的读取路径


- 教训别手抖乱点提示