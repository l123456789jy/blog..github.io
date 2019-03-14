---
layout:     post
title:      "android mediaplayer 设置倍速播放"
subtitle:   ""
date:       2019-03-14 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog: bug
tags:
    -
---


- 最近有一个老的项目之前使用的是mediaplayer，现在要想支持倍速播放，由于里面逻辑过于复杂，我记得之前mediaplayer是没有提供播放倍速的方法的，所以搜索android mediaplayer 设置倍速播放出来一大堆的使用其他三方库，知道用别的库，我还用找你，为了给后面小伙伴减少比较的困扰，悄悄告诉你们，现在Android已经支持播放倍速了，只不过的是Android6.0以上包含，按照目前的版本，已经大多数都已经是6.0以上。


- 使用方法![image.png](https://upload-images.jianshu.io/upload_images/1205414-b725a8b879bef475.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
地址;[https://developer.android.com/reference/android/media/MediaPlayer.html#setPlaybackParams(android.media.PlaybackParams)](https://developer.android.com/reference/android/media/MediaPlayer.html#setPlaybackParams(android.media.PlaybackParams))

- 是不是很简单只需要调用一个方法就行


     private void setPlayerSpeed(float speed){
        if (android.os.Build.VERSION.SDK_INT >=
       android.os.Build.VERSION_CODES.M) {
            PlaybackParams playbackParams = mediaPlayer.getPlaybackParams();
            playbackParams .setSpeed(speed);
            mediaPlayer.setPlaybackParams(playbackParams);
        }
    }

多看官方文档。