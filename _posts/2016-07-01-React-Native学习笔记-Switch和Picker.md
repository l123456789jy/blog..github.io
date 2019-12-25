---
layout:     post
title:      "React-Native学习笔记-Switch和Picker"
subtitle:   ""
date:       2016-07-01 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - Switch
    - Picker
---



- Switch是我们的一个选择开关控件
- Picker类似于按钮底部弹出的popwindow


## Switch属性方法介绍
- View相关属性样式全部继承(例如:宽和高,背景颜色,边距等相关属性样式)
- disabled bool 如果该值为true,用户就无法点击switch开关控件，默认为false
- onValueChange function 方法，当该组件的状态值发生变化的时候回调方法
- value bool 该开关的值，如果该值为true的时候，开关呈打开状态，默认为false


##Switch使用实例


```java

'use strict';
import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Switch,
} from 'react-native';
//引入
var RnDemo = React.createClass({
  getInitialState() {
    return {
      trueSwitchIsOn: true,
      falseSwitchIsOn: false,
    };
  },
  render() {
    return (
      <View style={styles.container}>
        <Text>
          Swtich实例
        </Text>
        <Switch
          disabled={true}
          onValueChange={(value) => this.setState({falseSwitchIsOn: value})}
          style={{marginBottom:10,marginTop:10}}
          value={this.state.falseSwitchIsOn} />
        <Switch
          disabled={false}
          onValueChange={(value) => this.setState({trueSwitchIsOn: value})}
          value={this.state.trueSwitchIsOn} />
      </View>
    );
  }
});

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
  }

});
AppRegistry.registerComponent('RnDemo', () => RnDemo);


```



##Picker选择器控件
### Picker属性方法
- View相关属性样式全部继承(例如:宽和高,背景颜色,边距等相关属性样式)
        - onValueChange  function方法,当选择器item被选择的时候进行调用。该方法被调用的时候回传入一下两个参数

        itemValue:该属性值为被选中的item的属性值
        itemPosition:该选择器被选中的item的索引position
        selectedValue: any任何参数值，选择器选中的item所对应的值，该可以是一个字符串或者一个数字
        style pickerStyleType 该传入style样式，设置picker的样式风格
        enabled bool 如果该值为false，picker就无法被点击选中。例如:用户无法进行做出选择
        mode enum ('dialog','dropdown')  选择器模式。在Android平台上面，设置mode可以控制用户点击picker弹出的样式风格
        'dialog': 该值为默认值，进行弹出一个模态dialog(弹出框)

        'dropdown':以picker视图为基础，在该视图下面弹出下拉框

        prompt string  设置picker的提示语(标题),在Android平台上面，模式设置成'dialog',显示弹出框的标题
