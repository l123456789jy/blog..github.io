---
layout:     post
title:      "React-Native学习笔记-TextInput"
subtitle:   ""
date:       2016-06-29 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - TextInput
---




- TextInput这个组件代表的意思就是输入框，和我们安卓中edtext是一样的。


##例子
- 这是一个简单的例子



```java
import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TextInput,
} from 'react-native';

<View style={styles.container}>
        <TextInput
          style={{height: 40, borderColor: 'gray', borderWidth: 1}}
          value={"设置显示的字体"}
        />
      </View>

```

这样运行就会在界面上显示一个默认的字体的输入框



##下面三个不同状态输入框



```java

class RnDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
        <TextInput style={{height:40,borderColor:'red',borderWidth:1}}
                   multiline={true} //代表可以输入多行
                   defaultValue='可以输入多行'
        />
        <TextInput
          style={{marginLeft:10,marginRight:10}}
          autoFocus={true}//自动获取焦点
          defaultValue='自动获取焦点'/>
        <TextInput
          editable={false}
          defaultValue='不可编辑状态'/>

      </View>

    );
  }
}

```


注意这个属性要输入在/的括号里面



##属性方法



- autoCapitalize  控制TextInput输入的字符进行切换成大写里面包含的属性：
        none:不自动切换任何字符成大写
        sentences:默认每个句子的首字母变成大写
        words:每个单词的首字母变成大写
        characters:每个字母全部变成大写





- autoCorrect  设置拼写自动修正功能 默认为开启(true)
- autoFocus 设置是否默认获取到焦点默认为关闭(false)
- defaultValue  给文本输入设置一个默认初始值。
- editable设置文本框是否可以编辑 默认值为true,可以进行编辑
- keyboardType  键盘类型


        (可选参数:"default", 'email-address', 'numeric', 'phone-pad', "ascii-capable", 'numbers-and-punctuation', 'url', 'number-pad', 'name-phone-pad', 'decimal-pad', 'twitter', 'web-search') 该用来选择默认弹出键盘的类型例如我们甚至numeric就是弹出数字键盘。鉴于平台的原因如下的值是所有平台都可以进行通用的

- maxLength  可以限制文本输入框最大的输入字符长度
- multiline  设置可以输入多行文字，默认为false(表示无论文本输入多少，都是单行显示)
- onBlur  function 监听方法，文本框失去焦点回调方法
- onChange function 监听方法,文本框内容发生改变回调方法
- onChangeText  function监听方法，文本框内容发生改变回调方法，该方法会进行传递文本内容
- onEndEditing  function监听方法，当文本结束文本输入回调方法
- onFocus  function 监听方法  文本框获取到焦点回调方法
- onLayout  function监听方法  组价布局发生变化的时候调用，调用方法参数为 {x,y,width,height}
- .onSubmitEditing function监听方法，当编辑提交的时候回调方法。不过如果multiline={true}的时候，该属性就不生效
- placeholder string 当文本输入框还没有任何输入的时候，默认显示信息，当有输入的时候该值会被清除
- placeholderText Color  string 设置默认信息颜色(placeholder)
- secureTextEntry  bool 设置是否为密码安全输入框 ，默认为false
- style 风格属性  可以参考Text组件风格
- value  string 输入框中的内容值
- numberOfLines number设置文本输入框行数，该需要首先设置multiline为true,设置TextInput为多行文本。
- textAlign 设置文本横向布局方式 可选参数('start', 'center', 'end')
- .textAlignVertical 设置文本垂直方向布局方式 可选参数('top', 'center', 'bottom')
- underlineColorAndroid  设置文本输入框下划线的颜色



##实现



```java

import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  TextInput,
} from 'react-native';

class RnDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Image
          style={styles.style_image}
          source={{uri:'http://pic27.nipic.com/20130327/12124306_143913492166_2.jpg'}}/>
        <TextInput
          style={styles.style_userinput}
          placeholder="QQ号/邮箱/手机号"//这个属性只要已输入就会自动消失
          numberofLines={1} //限制一行
          autoFocus={true}//自动获取焦点
          underlineColorAndroid={'transparent'}//输入框的线框为透明
        />
        <TextInput
          style={styles.style_userinput}
          placeholder="Q请输入密码"//这个属性只要已输入就会自动消失
          numberofLines={1} //限制一行
          autoFocus={true}//自动获取焦点
          underlineColorAndroid={'transparent'}//输入框的线框为透明
          secureTextEntry={true}//显示输入的为密码
        />
        <View style={styles.style_longin}>
          <Text style={{color:'#ffffff'}}>
            登录
          </Text >

        </View>
        <View style={styles.style_bottom}>
          <Text style={styles.style_view_unlogin}>
            无法登录?
          </Text>
          <Text style={styles.style_view_register}>
            新用户
          </Text>

        </View>


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
  style_view_unlogin:{
    fontSize:12,
    color:'#63B8FF',
    marginLeft:10,
  },
  style_view_register:{
    fontSize:12,
    color:'#63B8FF',
    marginRight:10,
    alignItems:'flex-end',
    flex:1,
    textAlign:'right',//文字靠右
  }

});
AppRegistry.registerComponent('RnDemo', () => RnDemo);

```
