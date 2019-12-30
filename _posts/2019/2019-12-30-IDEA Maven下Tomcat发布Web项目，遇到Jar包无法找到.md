---
layout:     post
title:      "IDEA Maven下Tomcat发布Web项目，遇到Jar包无法找到"
subtitle:   ""
date:       2019-12-30 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - idea
---


- 我们在Maven中配置的lib是没有自动下载到web中的lib目录下面的，默认下载在本地的Maven仓库，我们如果需要发布版本的时候一起打进lib下需要如下配置






    <dependencies>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>






![微信截图_20191230153151.png](http://ww1.sinaimg.cn/large/9f723435ly1gaesa5797cj20rp0ncab3.jpg)


- 需要点击图中右侧Available 按钮，选中需要导入jar包，put into 到 web lib 即可



