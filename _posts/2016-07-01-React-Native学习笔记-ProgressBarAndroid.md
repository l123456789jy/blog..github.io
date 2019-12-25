---
layout:     post
title:      "React-Native学习笔记-ProgressBarAndroid"
subtitle:   ""
date:       2016-07-01 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - ProgressBarAndroid
---




##ProgressBarAndroid的例子

```java
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TextInput,
  ProgressBarAndroid,
} from 'react-native';

class RnDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
        <ProgressBarAndroid
          styleAttr="Inverse"
        />

      </View>

    );
  }
}

```

注意要引入组件





##属性方法

- color  设置进度的颜色属性值
- indeterminate 设置是否要显示一个默认的进度信息，该如果styleAttr的风格设置成Horizontal的时候该值必须设置成false
- .progress  置当前的加载进度值(该值在0-1之间)
- styleAttr    进度条框的风格 ，可以取的值如下:

        Horizontal
        Small
        Large
        Inverse
        SmallInverse
        LargeInverse



##综合实例



```java


import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TextInput,
  ProgressBarAndroid,
} from 'react-native';

class RnDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>
          ProgressBarAndroid控件实例
        </Text>
        //红色圆形的进度条
        <ProgressBarAndroid  color="red" styleAttr='Inverse'/>
        //绿色的水平进度条，有默认值
        <ProgressBarAndroid  color="green" styleAttr='Horizontal' progress={0.2}
                             indeterminate={false} style={{marginTop:10}}/>
        //绿色的水平进度条，没有默认值
        <ProgressBarAndroid  color="green" styleAttr='Horizontal'
                             indeterminate={true} style={{marginTop:10}}/>
        //大小
        <ProgressBarAndroid  color="black" styleAttr='SmallInverse'
                             style={{marginTop:10}}/>
        <ProgressBarAndroid  styleAttr='LargeInverse'
                             style={{marginTop:10}}/>

      </View>

    );
  }
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



