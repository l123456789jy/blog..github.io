---
layout:     post
title:      "在Windows上搭建Flutter开发环境"
subtitle:   ""
date:       2018-05-09 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Flutter
---



#### 1.中文文档：https://flutterchina.club/setup-windows/




#### 2.英文文档 ：https://flutter.io/get-started/install/





#### 3.中国国内使用该镜像：https://github.com/flutter/flutter/wiki/Using-Flutter-in-China





#### `Flutter`类似`Rn`这种以淘代码适配多个平台，谷歌出品。




#### 4.我们使用这个链接下载`https://storage.googleapis.com/flutter_infra/releases/beta/windows/flutter_windows_v0.3.2-beta.zip`




#### 5.下在完毕之后找到文件的`flutter_console.bat`内部flutter目录。通过双击启动它



#### 6.在Flutter控制台中，运行`flutter doctor`命令以查看是否需要安装任何依赖项来完成设置

#### 7.这里下载工具推荐使用`aria2`
![](http://ww1.sinaimg.cn/large/9f723435ly1fr52kc7x18j20yw0blq3j.jpg)

![](http://ww1.sinaimg.cn/large/9f723435ly1fr52wkz4btj20q80dct8r.jpg)当我们打开`flutter_console.bat`就会打开子控制台

#### 8.执行`flutter doctor`更新

#### 9.下载安装`Androidstudio`插件
- 需要安装两个插件:
- Flutter插件： 支持Flutter开发工作流 (运行、调试、热重载等).
- Dart插件： 提供代码分析 (输入代码时进行验证、代码补全等).

- 启动Android Studio.
- 打开插件首选项 (Preferences>Plugins on macOS, File>Settings>Plugins on Windows & Linux).

- 选择 Browse repositories…, 选择 Flutter 插件并点击 install.

- 重启Android Studio后插件生效.
![](https://ws1.sinaimg.cn/mw690/9f723435gy1fr535nattwj20n70ewaba.jpg)
![](https://ws1.sinaimg.cn/large/9f723435ly1fr53dqxxpvj20i30hsjs4.jpg)
这里需要注意下载此插件需要挂代理

#### 10.接下来就相当于创建一个Android项目一样创建
![](https://ws1.sinaimg.cn/large/9f723435ly1fr53pa7ikbj20bb08cdg0.jpg)
运行Android工程一样运行
![](https://ws1.sinaimg.cn/large/9f723435ly1fr53vj460wj21370min0w.jpg)
![](https://ws1.sinaimg.cn/large/9f723435ly1fr53wtwxn3j20ah0i4wen.jpg)

##### 10.最重要的热更新我们如何去做呢？
![](https://ws1.sinaimg.cn/large/9f723435ly1fr544t41h7j20c60nqmyt.jpg)
点击reload按钮，即可立即更新，不需要重新打包编译。



