---
layout:     post
title:      "jsdelivr cdn优化githubpage"
subtitle:   ""
date:       2019-12-26 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - cdn
---


- jsdelivr cdn优化githubpage

- jsdelivr 是一个免费的CDN网站，提供一些前端库的CDN加速，主要是有中国节点，我的博客用的是githubpage默认的cdn都是在日本访问速度上比较慢，而jsdelivr还有一个特点就是支持github上资源加速。


![微信截图_20191226084115.png](http://ww1.sinaimg.cn/large/9f723435ly1ga9ty3jooyj20vf0getbv.jpg)

- 我们可以看到中国基本所有一线城市都有节点,github加速规则是这样

https://cdn.jsdelivr.net/gh/你的用户名/你的仓库名@发布的版本号/文件路径

https://cdn.jsdelivr.net/gh/l123456789jy/l123456789jy.github.io/css/hux-blog.min.css

比如我要加速我github仓库上这个CSS，上面写就可以了，仓库一定要是公共的，不能使私有的私有无效，我们看下是不是走的中国节点

![微信截图_20191226084809.png](http://ww1.sinaimg.cn/large/9f723435ly1ga9u4qkuatj20lg0astbc.jpg)

可以看到走的是国内，这样就可以把githubpage上所有的css,图片都可以换上jsdelivr，看下谷歌测试之后网站评分
https://liujingyuan.top/


![微信截图_20191226083906.png](http://ww1.sinaimg.cn/large/9f723435ly1ga9u6sot5dj20np09274f.jpg)

效果还是不错的，如果引用的npm上可以在这里搜搜提供好的远程cdn

https://www.jsdelivr.com/









