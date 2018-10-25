---
layout:     post
title:      "通过VPS配合youtube-upload自动上传视频到youtube"
subtitle:   ""
date:       2018-10-25 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:
tags:
    -
---


通过VPS配合youtube-upload自动上传视频到youtube

#### 由于家里网络不稳定，需要上传视频到youtube，于是就打算配置下

- 打开该项目地址 https://github.com/tokland/youtube-upload
- 支持Python 2.6/2.7/3.x，没有python需要自行安装
- pip install --upgrade google-api-python-client oauth2client progressbar2

		$ wget https://github.com/tokland/youtube-upload/archive/master.zip
		$ unzip master.zip
		$ cd youtube-upload-master
		$ sudo python setup.py install

- 经过以上步奏已经下载配置完毕，由于需要使用新的谷歌OAuth 2.0 认证，需要去谷歌创建个项目。
- 打开此地址：https://console.developers.google.com/projectselector/apis/credentials?supportedpurview=project  创建个自己的项目

![](https://ws1.sinaimg.cn/large/9f723435ly1fwkju6hkm9j20bl096q3a.jpg)
点击标记部分，进去之后随便输入个项目名字，其他空着不填写就可以，然后下载文件

![](https://ws1.sinaimg.cn/large/9f723435ly1fwkjvgpyodj20x00573yl.jpg)

- 打开VPS将当前下载的文件上传到VPS重命名为 client_secrets.json

- 存放到root目录下面，确保你当前目录下面有个aaa的视频执行以下命令

- youtube-upload --title="测试python自动上传视频" --description="我是描述" --tags="标签"  --client-secrets=client_secrets.json ~/aaa.mp4

- 接下来会看到弹出个网址，粘贴这个网址在浏览器打开复制验证码

![](https://ws1.sinaimg.cn/large/9f723435ly1fwkjyp6lxpj20ea0i5dgw.jpg)

![](https://ws1.sinaimg.cn/large/9f723435ly1fwkjzpg2guj20vv0353yl.jpg)

可以看倒成功了，这里需要注意的是，需要手动开启youtube的API权限，否则会提示

![](https://ws1.sinaimg.cn/large/9f723435ly1fwkk0yen50j213d070gm6.jpg)

然后根据提示的网址开启权限就可以了


![](https://ws1.sinaimg.cn/large/9f723435ly1fwkk5o62l8j20p00jlq9c.jpg)