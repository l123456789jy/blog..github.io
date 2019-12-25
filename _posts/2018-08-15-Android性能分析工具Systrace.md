---
layout:     post
title:      "Android性能分析工具Systrace"
subtitle:   ""
date:       2018-08-15 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 逆向
---


- 这里只记录自己这次试用记录，具体文档官网已经有了
- 现在`AS3.1`以上已经没有，打开`monitor`入口，只能进入到`androidsdk\tools`目录下面手动打开
- 我们打开`monitor’之后
![](http://ww1.sinaimg.cn/large/9f723435ly1fua8a91lokj20dv02za9y.jpg)
![](http://ww1.sinaimg.cn/large/9f723435ly1fua8auivijj20eh0nvwf8.jpg)
- 点击`ok`,然后你就直接操作你需要测试的应用界面就可以
- 上面的时间是5秒，看到他生成的路径在`C`盘，这个文件只能`chrome`打开，我们直接拖到谷歌浏览器打开

![](http://ww1.sinaimg.cn/large/9f723435ly1fua8g9c89jj213e0buq3s.jpg)

- 我们可以看到上面的界面

- 这里我们只需要知道两个快捷键
- `w`和 `s`放大和缩小
- 当我我么你需要移动的时候直接点击
![](http://ww1.sinaimg.cn/large/9f723435ly1fua8hudf63j203s06amwy.jpg)
这个按钮就可以了

- 我们只关注`UI THREAD`,我们找到红色的地方，然后点击就可以看到具体的耗时时间和建议

![](http://ww1.sinaimg.cn/large/9f723435ly1fua8jyv521j20qe0d03yw.jpg)

