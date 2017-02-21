---
layout:     post
title:      "android上实现离线缓存播放加密HLS视频和未加密的HLS视频"
subtitle:   ""
date:       2016-07-24 15:07:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - HLS
---









1.首先什么是HLS格式的视频，大家去谷歌下就知道了。





 2.我们知道HLS格式的视频，只有安卓4.0以上才支持，目前基本4.0一下的机子基本可以考虑，不兼容了，所以为了减少工作量，就没有打算使用三方的播放器，就继续使用MediaPlayer来进行播放





3.HLS格式的视频，他是通过一个m3u8文件，然后里面包含若干个TS文件片段，这里有个苹果的官方的一个例子：http://devimages.apple.com/iphone/samples/bipbop/gear1/prog_index.m3u8
毕竟这个是苹果研发的所以看官方的格式肯定没错。


##4.我们看看里面的内容

        #EXTM3U
        #EXT-X-TARGETDURATION:10
        #EXT-X-MEDIA-SEQUENCE:0
        #EXTINF:10, no desc
        fileSequence0.ts
        #EXTINF:10, no desc
        fileSequence1.ts
        #EXTINF:10, no desc
        fileSequence2.ts
        #EXTINF:10, no desc
        fileSequence3.ts
        #EXTINF:10, no desc
        fileSequence4.ts
        #EXTINF:10, no desc
        fileSequence5.ts
        #EXTINF:10, no desc
        fileSequence6.ts
        #EXTINF:10, no desc
        fileSequence7.ts

我们可以看到里面他又一个一个ts视频片段，这个一个一个视频片段就是我们需要的播放，那么他是如何被播放器识别播放的呢。


##5.其实上面的这些关键的字段都是约定好的，所以在MediaPlayer这个类的native层，会去按照规定好的字段去解析这个m3u8文件，那么他在播放器是最终播放的地址是怎么样的呢，是这样的

    http://devimages.apple.com/iphone/samples/bipbop/gear1/fileSequence0.ts
    组拼起来的，你可以直接用这个地址播放看看。



##6.实现这种未加密的缓存还是比较好实现的，大概可以分为这几步。
      1.我们首先按照特定的格式去解析这m3u8文件。
      2.按照解析出来的ts文件按照我们知道的规则组拼起来，其下载这些ts文件，存放在手机的sd卡
      3.我们需要在本地搭建一个本地http服务器，我们之前本打算搭建一个https，但是由于生成的证书是自己生成导致播放器不去访问本地的服务器。
      4.本地服务器我们通过过滤特定的接口名字，来实现根据不同ts名字返回不同的视频文件（这里最好生成和原始的ts文件的名字一样）
      5.我们如何知道播放器播完一段视频呢，因为他是一段一段播放的，所以这里就需要我们在本地生成一份本地指向我们本地服务器的m3u8文件，直接播放本地的时候直接返给播放器，他就会在播放一段视频快完毕的时候请求下一个接口，由于这个接口是指向本地的所以，我们就可以直接把本地的视频返给播放器了。


以上就实现了缓存播放未加密的HLS视频的方法。



##7.那么如何实现加密的呢？我们看下加密的m3u8文件的格式

    #EXTM3U
    #EXT-X-VERSION:3
    #EXT-X-KEY:METHOD=AES-128,URI="http://xxxxxx:5555//test/1102/test/segments.key"
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-ALLOW-CACHE:YES
    #EXT-X-TARGETDURATION:19
    #EXTINF:13.966667,
    http://xxxxxx:5555/test/1102/test/segments0.ts
    #EXTINF:10.000000,
    http://xxxxxx:5555/test/1102/test/segments1.ts
    #EXTINF:10.000000,
    http://xxxxxx:5555/test/1102/test/segments2.ts
    #EXTINF:10.000000,
    http://xxxxxx.cn:5555/test/1102/test/segments3.ts
    #EXTINF:10.000000,
    http://xxxxxxn.cn:5555/test/1102/test/segments4.ts
    #EXTINF:7.033333,
    http://xxxxxx:5555/test/1102/test/segments5.ts
    #EXTINF:10.000000,


我们看到了多了个字段EXT-X-KEY，这个也是m3u8给规定好的加密字段，如果包含这个字段播放器就会先去请求这个key，然后拿这个这个key去访问加密的TS视频就可以播放了。
  其实看到这我们就因该有思路怎么去做，加密的缓存播放了。



##8,实现播放加密缓存的思路
      1.我们首先按照特定的格式去解析这m3u8文件。
      2.按照解析出来的ts文件按照我们知道的规则组拼起来，其下载这些ts文件，存放在手机的sd卡，这些下载下来的TS视频文件是播放不了的，再把正确的key下载下来。
      3.我们需要在本地搭建一个本地http服务器，我们之前本打算搭建一个https，但是由于生成的证书是自己生成导致播放器不去访问本地的服务器。
      4.本地服务器我们通过过滤特定的接口名字，来实现根据不同ts名字返回不同的视频文件（这里最好生成和原始的ts文件的名字一样）
      5.我们如何知道播放器播完一段视频呢，因为他是一段一段播放的，所以这里就需要我们在本地生成一份本地指向我们本地服务器的m3u8文件，直接播放本地的时候直接返给播放器，这里多了我们需要多个加密的key。我们本地增加一个过滤接口的方法，判断如果是访问加密的key就先把key返给播放器，然后播放器会紧接着去请求拿去ts视频这样我们就可以把视频返给他，它内部就会去解密进行播放了。


解析工具已经上传：https://github.com/l123456789jy/Lazy/blob/master/lazylibrary/src/main/java/com/github/lazylibrary/util/M3U8ParserUtiles.java
