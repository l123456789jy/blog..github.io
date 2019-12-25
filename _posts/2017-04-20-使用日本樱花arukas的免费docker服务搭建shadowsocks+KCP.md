---
layout:     post
title:      "使用日本樱花arukas的免费docker服务搭建shadowsocks+KCP"
subtitle:   ""
date:       2017-04-20 15:32:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - arukas
---


- 先去樱花官网注册地址 https://app.arukas.io/
- 注册成功之后我们进入到控制台，点击Greate按钮
![image](http://ws3.sinaimg.cn/large/9f723435ly1fetac12xwlj20vb0crdge.jpg)

- 进入到创建VPS配置界面，输入下面界面的参数
![image](http://ws1.sinaimg.cn/large/9f723435ly1fetae1b0w2j20ah0ilmzp.jpg)

- 此处我们使用已经安装了配置了shadowsocks+KCP的docter容器``endoffight/shadowsocks-kcp`

```    剩下参数按照上图添加

- 创建成功之后就显示为下面状态

![image](http://wx3.sinaimg.cn/large/9f723435ly1fetajci399j20s10ixwgb.jpg)

- ip如何获取呢，例如：

http://seaof-153-125-239-208.jp-tokyo-27.arukascloud.io:31527 (3721/tcp)
他的ip 就是  153.125.239.208:31527  下面的9个镜像都可以使用










- 如果没有修改这个容器的默认SS配置如下：


    IP地址：参考端口映射
    SS端口：对应3721/TCP的端口，如果使用UDP转发，端口为3721/UDP所对应的值
    SS密码：laogao
    SS加密：aes-256-cfb
    SS超时：300



此处的数据是填写在Android上的SS软件上


![image](http://ws3.sinaimg.cn/large/9f723435ly1fetarie1rij20u01hcgot.jpg)

![image](http://wx3.sinaimg.cn/large/9f723435ly1fetaqxzgu1j20u01hc79d.jpg)



- 这里需要注意如果要使用KCP加速需要去下载插件搜索下就行了，插件填写参数如下不用修改：
        dscp=46;parityshard=3;key=phpgao;mode=fast;mtu=1350;rcvwnd=1024;datashard=15;sndwnd=1024;crypt=aes-192



- 如果使用了KCP ss上服务器的端口要是对应3824/UDP的端口,不用KCP加速直接使用后面跟的TCP端口就行。


- KCP默认配置、

        远程IP：参考端口映射
        远程端口：对应3824/UDP的端口
        TCP密码：phpgao
        SS加密：aes-192
        SS模式：fast