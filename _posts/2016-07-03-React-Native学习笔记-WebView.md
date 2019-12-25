---
layout:     post
title:      "React-Native学习笔记-WebView"
subtitle:   ""
date:       2016-07-03 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - WebView
---




##WebView
- 这该WebView组件进行创建渲染一个原生的WebView，进行加载一个网页


##属性方法
- automaticallyAdjustContentInsets bool   设置是否自动调整内容
- contentInset  {top:number,left:number,bottom:number,right:number}  设置内容所占的尺寸大小
- html  string  WebView加载的HTML文本字符串
- injectJavaScript  string 当网页加载之前进行注入一段js代码
- onError function  方法 当网页加载失败的时候调用
- onLoad  function 方法  当网页加载结束的时候调用
- onLoadEnd fucntion 当网页加载结束调用，不管是成功还是失败
- onLoadStart  function  当网页开始加载的时候调用
- onNavigationStateChange function方法  当导航状态发生变化的时候调用
- renderError  function  该方法用于渲染一个View视图用来显示错误信息
- renderLoagin function  该方法用于渲染一个View视图用来显示一个加载进度指示器
- url  string  设置加载的网页地址
- domStorageEnabled bool  该适合Android平台 该只适合于Android平台，用于控制是否开启DOM Storage（存储）
- avaScriptEnabled  bool  该适合于Android平台，是否开启javascript


##实例代码



```java
'use strict';
import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TouchableOpacity,
  WebView,
} from 'react-native';
var DEFAULT_URL = 'https://github.com/l123456789jy';

var RnDemo = React.createClass({
  render: function() {
    return (
      <View style={{flex:1}}>
        <Text style={{height:40}}>简单的网页显示</Text>
        <WebView style={styles.webview_style}
                 source={{uri:DEFAULT_URL}}
                 startInLoadingState={true}
                 domStorageEnabled={true}
                 javaScriptEnabled={true}
        >
        </WebView>
      </View>
    );
  },
});
var styles = StyleSheet.create({
  webview_style:{
    backgroundColor:'#00ff00',
  }
});

AppRegistry.registerComponent('RnDemo', () => RnDemo);


```
