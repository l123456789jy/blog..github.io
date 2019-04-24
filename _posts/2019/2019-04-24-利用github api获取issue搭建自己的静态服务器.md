---
layout:     post
title:      "利用github api获取issue搭建自己的静态服务器"
subtitle:   ""
date:       2019-04-24 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog: gradle
tags:
    -
---
- 有时候我们如果只是搭建个简单的静态库，还要用去买服务器就太麻烦有没有简单的方式那就是github的issue我们可以使用



- 基本使用
// api
https://api.github.com/repos/:username/:repository/issues
上面是最基本的api链接，比如我的：

  // api
[https://api.github.com/repos/l123456789jy/yangmao/issues?page=1&per_page=10](https://api.github.com/repos/l123456789jy/yangmao/issues?page=1&per_page=10)




![image.png](https://upload-images.jianshu.io/upload_images/1205414-9e90aad28f5d2ecd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/1205414-c102acc4df4f8849.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 通过上面就可以访问了，然后解析了，但是这个没带token是有访问次数限制的


- 如果需要增加访问次数就需要添加token，[https://github.com/settings/tokens](https://github.com/settings/tokens)

- 添加完就可以这样访问了

https://api.github.com/repos/l123456789jy/yangmao/issues?[access_token=<access_token>]&page=<page>&per_page=<per_page>
只需要在我们项目的根目录执行以上命令格式就可以，就是告诉gradle你要使用的gradle.bat的目录即可，是不是方便了许多