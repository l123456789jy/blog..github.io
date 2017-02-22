---
layout:     post
title:      "React-Native学习笔记-DrawerLayoutAndroid"
subtitle:   ""
date:       2016-07-03 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - DrawerLayoutAndroid
---




##该DrawerLayoutAndroid组件封装了Android平台的DrawerLayout控件，常常用于侧滑栏
- 导航栏的视图在屏幕中一开始是隐藏的，但是我们可以通过drawerPostition指定位置进行把导航视图拖拽出来，最终拖拽出来的距离大小可以使用drawerWidth属性进行

- 基本使用：


```java


import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,


  ##基本属性：
  - drawerPosition   参数为枚举类型
  Text,
  View,
  Image,
  TextInput,
  ProgressBarAndroid,
  DrawerLayoutAndroid,
} from 'react-native';

class RnDemo extends Component {
  render() {
    var navigationView = (
      <View style={{flex: 1, backgroundColor: '#fff'}}>
        <Text style={{margin: 10, fontSize: 15, textAlign: 'left'}}>I'm in the Drawer!</Text>
      </View>
    );
    return (
      <DrawerLayoutAndroid
        drawerWidth={300}
        drawerPosition={DrawerLayoutAndroid.positions.Left}
        renderNavigationView={() => navigationView}>
        <View style={{flex: 1, alignItems: 'center'}}>
          <Text style={{margin: 10, fontSize: 15, textAlign: 'right'}}>Hello</Text>
          <Text style={{margin: 10, fontSize: 15, textAlign: 'right'}}>World!</Text>
        </View>
      </DrawerLayoutAndroid>
    )

  };
}

const styles = StyleSheet.create({
  container: {
    flex: 1,  //定义了flex属性标示当前的属性的可伸缩的
    backgroundColor: '#efefef',
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
  }

});
AppRegistry.registerComponent('RnDemo', () => RnDemo);


```


##基本属性：
- drawerPosition   参数为枚举类型(DrawerConsts.DrawerPosition.Left, DrawerConsts.DrawerPosition.Right)

进行指定导航菜单用那一侧进行滑动出来，根据官方实例最终传入的两个枚举值分别    为:DrawerLayoutAndroid.positions.Left和DrawerLayoutAndroid.positions.Right类型指定在那边打开


- drawerWidth  进行指定导航菜单视图的宽度，也就是说该侧面导航视图可以从屏幕边缘拖拽到屏幕的宽度距离



- keyboardDismissMode    参数为枚举类型('none','on-drag') 进行指定在导航视图拖拽的过程中是否要隐藏键盘
        none   (默认值),默认不会隐藏键盘
        on-drag  当拖拽开始的时候进行隐藏键盘


- onDrawerClose   function 方法 当导航视图被关闭后进行回调该方法
- onDrawerOpen   function 方法 当导航视图被打开后进行回调该方法
- onDrawerSlide  function  方法  当导航视图和用户进行交互的时候调用该方法
- onDrawerStateChanged function方法，该当导航视图的状态发生变化的时候调用该方法。该状态会有以下三种状态

        idle (空闲) 表示导航视图上面没有任何交互状态
        dragging (正在拖拽中)   表示用户正在和导航视图产生交互动作
        settling (暂停-刚刚结束)  表示用户 刚刚结束和导航视图的交互动作，当前导航视图正在打开或者关闭拖拽滑动动画效果




- renderNavigationView  function 方法，该方法进行渲染一个导航抽屉的视图(用于用户从屏幕边缘拖拽出来)


