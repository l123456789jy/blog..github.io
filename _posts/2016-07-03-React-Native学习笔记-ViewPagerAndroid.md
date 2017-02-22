---
layout:     post
title:      "React-Native学习笔记-ViewPagerAndroid"
subtitle:   ""
date:       2016-07-03 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - ViewPagerAndroid
---





- ViewPagerAndroid就是我们安卓中的viewpager轮播图
- 【特别注意】ViewPagerAndroid中的所有子View必须为<View>控件，不能为复合型的组件。你可以为每一个子视图添加列如:padding或则backgroundColor之类的属性。

##属性方法
- View相关属性样式全部继承(例如:宽和高,背景颜色,边距等相关属性样式)
- initialPage  number  ViewPagerAndroid初始索引页，不过我们可以使用setPage方法来更新页码，通过onPageSelected方法来监听页面滑动。
- keyboardDismissMode  enum('none','on-drag')  枚举类型，进行设置在拖拽滑动的过程中是否要显示键盘。
- onPageScroll  function 方法，该方法在页面进行滑动的时候执行(不管是因为页面滑动动画原因还是由于页面之间的拖拽以及滑动原因).该会回调传入的event.nativeEvent对象会有携带如下参数:

        'position'   从左起开始第一个可见的页面的索引

        'offset'  该value值的范围为[0,1)，该用来代表当前页面的却换的状态。值x代表该索引页面(1-x)的范围可见，另外x范围代表下一个页面可见的区域

- onPageScrollStateChanged  function 该回调方法会在页面滚动状态发生变化的时候进行调用。页面的滚动状态有下面三种情况:

        'idle' 该表示当前用户和页面滚动没有任何交互

        'dragging'  拖动中，该表示当前页面正在被拖拽滑动中
        'settling'   该表示存在页面拖拽或者滑动的交互。页面滚动正在结束。并且正在关闭或者打开动画。

        onPageSelected  function 方法 该在页面进行拖拽滑动切换完成之后回调。该方法回调参数中的event.nativeEvent对象会携带如下一个属性 : 'position'  该属性代表当前选中的页面的索引.


##应用实例


```java
'use strict';
import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Picker,
  ViewPagerAndroid,
} from 'react-native';
//引入
class RnDemo extends Component {
  render() {
    return (
      <View >
        <Text style={styles.welcome}>
          ViewPagerAndroid实例
        </Text>
        <ViewPagerAndroid style={styles.pageStyle} initialPage={0}>
          <View style={{backgroundColor:"red"}}>
            <Text>First Page!</Text>
          </View>
          <View style={{backgroundColor:"yellow"}}>
            <Text>Second Page!</Text>
          </View>
        </ViewPagerAndroid>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,  //定义了flex属性标示当前的属性的可伸缩的
    backgroundColor: '#efefef',
    justifyContent: 'center',
    alignItems: 'center',
    height: 1280,
  },
  style_bottom: {
    flex: 1,
    flexDirection: 'row',//代表一行
    alignItems: 'flex-end',//在底部
    bottom: 10,//距离底部10
  },
  style_longin: {
    marginTop: 15,
    marginLeft: 10,
    marginRight: 10,
    backgroundColor: '#63B8FF',
    height: 35,
    borderRadius: 5,
    alignItems: 'center',//内部控件居中
    justifyContent: 'center',//子控件位于父容器的居中
  },
  style_userinput: {
    marginTop: 10,
    backgroundColor: '#FFFFFF',
    height: 35,
    textAlign: "center"
  },
  style_image: {
    borderRadius: 35,
    height: 70,
    width: 70,
    marginTop: 40,
    alignSelf: 'center',
  },
  style_view_unlogin: {
    fontSize: 12,
    color: '#63B8FF',
    marginLeft: 10,
  },
  style_view_register: {
    fontSize: 12,
    color: '#63B8FF',
    marginRight: 10,
    alignItems: 'flex-end',
    flex: 1,
    textAlign: 'right',//文字靠右
  },
  pageStyle: {
    alignItems: 'center',
    padding: 20,
    height:200,
  }

});
AppRegistry.registerComponent('RnDemo', () => RnDemo);


```
