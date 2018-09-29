---
layout:     post
title:      "通过xposed脱壳APK"
subtitle:   ""
date:       2018-08-15 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 逆向
---


####  通过xposed脱壳APK

- 在看到`VirtualApp`的RDM上，说可以用于脱壳APK，但是技术不公布，想了想，确实都已经可以模拟出APK运行环境了，在加载APK的时候，得到`dex`那还不是顺手的事情，但是这个肯定关于利益问题就没公开。

- 思路：通过Hook ClassLoader的loadClass方法，反射调用getDex方法取得Dex(com.android.dex.Dex类对象)，再将里面的dex写出，，就hook了一个方法而已

- [源码](https://github.com/fooree/fooXposed/blob/1e16bc52b36a9994dc00a13831c3e83a04845914/DumpDex/src/main/java/foo/ree/demos/dumpdex/DexHook.java "源码")

- 这里运行环境要求，必须装`xposted`所以需要`root`

- 经过我的测试目前360加固可以破解，爱加密试了，不行，因为爱加密是把方法都掏空，真正源码放到so中，其他没有测试，但是360已经针对`xposted`检测了，如果安装了就强制退出

![](http://ww1.sinaimg.cn/large/9f723435ly1fu9cujj46kj20vs06l3yz.jpg)

- 这样以后小型的APK都做了这样措施，岂不是`xposted`都用不了了，道高一尺魔高一丈，只是以后破解会增加难度。
