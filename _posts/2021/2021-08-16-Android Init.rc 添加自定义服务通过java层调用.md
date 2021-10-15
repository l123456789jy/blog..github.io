---
layout:     post
title:      "Android Init.rc 添加自定义服务通过java层调用"
subtitle:   ""
date:       2021-08-16 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags: Android
    -
---

- 有时候我们需要执行一些需要root权限功能，而又不希望放开root权限，就可以从Init.rc启动一些服务，他就具有root权限
- 按照rc语法格式添加新增service
- 在init.rc文件中添加如下

```



 最后两个必写，其他的省略亦可

service screencnap/system/bin/screencap
# class 包括core main late_start等，可以不设置。默认default
class main
# 用户属性，默认root，一般给root即可，权限最大
user root
# 所属组，默认root
group root
# disabled 即通过class不能启动，只能start screencap 这种name的方式启动
disabled
# 只运行一次
oneshot

服务启动条件

on property:sys.start_service=1
start screencap

- 这里的bin可以是sh执行文件也可以是C编译出来的二进制文件，需要配置自动拷贝到system/bin目录下面
- 我们还需要配置**SELinux**访问权限
- 添加te文件，目录位置大概为device目录下面，我们打开device.mk找到，BOARD_SEPOLICY_DIRS 属性，这里配置我们加载的sellinux的权限目录位置，这里自己定义目录位置，这里权限文件一般是是file_contexts和自己定义的脚本名字.te
- ```
  
  ```

#file_contexts 文件添加如下
/system/bin/screencap u:object_r:screencap_exec:s0

```

```

#screencap.te 整个文件如下
type screencap, domain,coredomain;
type screencap_exec, exec_type,vendor_file_type,file_type;

init_daemon_domain(screencap)

```

```

- 配置完成以后，我们进入shell，执行 ps -A 或者top 看下当前定义的进程有没有启动就知道，是否配置成功
- 然后，在java中调用方式为

```
SystemProperties.set("xxx", "xxx");  即可，如果是脚本
```

SystemProperties.set("ctl.start*", "xxx"); ，即可

```

```
参考文章： https://www.it610.com/article/1289059738746691584.htm