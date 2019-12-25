---
layout:     post
title:      "React-Native学习笔记-Image"
subtitle:   ""
date:       2016-07-03 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - Image
---



- 要使用Image这个控件我们需要在最上面引入这个model

```java
import React, {
    AppRegistry,
    Component,
    StyleSheet,
    Text,
    View,
    Image//引入
} from 'react-native';


```




##加载本地图片

```css
<View  style={{marginLeft:10,marginTop:10}}>
      <Text style={{fontSize:16}}>'测试本地图片'</Text>
      <Image source={require('./img/my_icon.png')} />
 </View>

```

##加载工程中drawable中的图片


```java
<Image source={{uri:'ic_launcher'}} style={{width: 40, height: 40}} />

```

这里的图片一定要放在drawable下


##加载网络的图片

```java
<Image source={{uri:'http://mta.zttit.com:8080/images/ZTT_1404756641470_image.jpg'}}  style={{width:100,height:100}}/>

```

##texview加图片的背景

```java
<Image source={require('./img/my_icon.png')} >
           <Text style={{color:'red'}}>下面是背景图</Text>
</Image>

```

##image属性的方法
- onLayout   (function) 当Image布局发生改变的，会进行调用该方法，调用的代码为: {nativeEvent: {layout: {x, y, width, height}}}.
- onLoad (function):当图片加载成功之后，回调该方法
- onLoadEnd (function):当图片加载失败回调该方法，该不会管图片加载成功还是失败
- onLoadStart (fcuntion):当图片开始加载的时候调用该方法
- resizeMode  缩放比例,可选参数('cover', 'contain', 'stretch') 该当图片的尺寸超过布局的尺寸的时候，会根据设置Mode进行缩放或者裁剪图片
- source {uri:string} 进行标记图片的引用，该参数可以为一个网络url地址或者一个本地的路径

##image样式风格
- FlexBox  支持弹性盒子风格
- Transforms  支持属性动画
- resizeMode  设置缩放模式
- backgroundColor 背景颜色
- borderColor     边框颜色
- borderWidth 边框宽度
- borderRadius  边框圆角
- overflow 设置图片尺寸超过容器可以设置显示或者隐藏('visible','hidden')
- tintColor  颜色设置
- opacity 设置不透明度0.0(透明)-1.0(完全不透明)
- alignSelf:center 代表在当前的布局中居中
- flexDirection:row  这个属性代表是以横向布局





##下面是一个例子


```java
import React, {
    AppRegistry,
    Component,
    StyleSheet,
    Text,
    View,
    Image
} from 'react-native';

class AwesomeProject extends Component {
    render() {
        return (
            <View style={styles.container}>
                <View style={{flexDirection:'row'}}>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                </View>
                <View style={{flexDirection:'row',marginTop:10}}>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                    <View style={{width:70}}>
                        <Image source={{uri:'http://p1.meituan.net/mmc/08615b8ae15d03c44cc5eb9bda381cb212714.png'}}
                               style={styles.imagestyle}/>
                        <Text style={styles.texstyle}>美食</Text>
                    </View>
                </View>
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        marginLeft: 5,
        marginTop: 10,
        marginRight: 5,
    },
    texstyle: {
        textAlign: 'center',
        color: '#555555',
        marginTop: 5,
        fontSize: 11,
    },
    imagestyle: {
        width: 45,
        height: 45,
        alignSelf: 'center',
    }
});

AppRegistry.registerComponent('AwesomeProject', () => AwesomeProject);



```
