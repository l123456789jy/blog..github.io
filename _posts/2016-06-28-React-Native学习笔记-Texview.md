---
layout:     post
title:      "eact-Native学习笔记-Texview"
subtitle:   ""
date:       2016-06-28 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - Texview
---




- 要使用Texview这个控件我们需要在最上面引入这个model

```java
import React, {
    AppRegistry,
    Component,
    StyleSheet,
    Text,//引入
    View
} from 'react-native';

```


- 下面我们呢就简单的使用下吧

```java

import React, {
    AppRegistry,
    Component,
    StyleSheet,
    Text,
    View
} from 'react-native';

class AwesomeProject extends Component {
    render() {
        return (
            <Text style={styles.content}>
                我是最外边的====
                <Text style={styles.instructions}>
                    我是里面的
                </Text>
            </Text>

        );
    }
}

const styles = StyleSheet.create({
    container: {
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#F5FCFF',
        fontWeight: "bold",
    },
    instructions: {
        textAlign: 'center',
        color: '#333333',
    },
});

AppRegistry.registerComponent('AwesomeProject', () => AwesomeProject);

```

- 根据显示的结果我们得知Texview是可以嵌套的而且他的样式不会被最外层的影响，这里需要注意下别写成==小写了==。



##主要的属性方法

- allowFontScaling (bool):控制字体是否根据iOS的设置进行自动缩放-iOS平台,Android平台不适用

- numberOfLines (number):进行设置Text显示文本的行数，如果显示的内容超过了行数，默认其他多余的信息就不会显示了。

- onLayout (function) 当布局位置发生变动的时候自动进行触发该方法, 其中该function的参数如下:[code lang="" start="" highlight=""]{nativeEvent: {layout: {x, y, width, height}}}[/code]

- onPress (fcuntion) 该方法当文本发生点击的时候调用该方法.


##可设置的样式不包括view所有的

- color:字体颜色
- .fontFamily 字体名称
- fontSize  字体大小
- fontStyle   字体风格(normal,italic)
- .fontWeight  字体粗细权重("normal", 'bold', '100', '200', '300', '400', '500', '600', '700', '800', '900')
- textShadowOffset 设置阴影效果{width: number, height: number}
- textShadowRadius 阴影效果圆角
- textShadowColor 阴影效果的颜色
- letterSpacing 字符间距
- lineHeight 行高
- textAlign   文本对其方式("auto", 'left', 'right', 'center', 'justify')
- textDecorationLine  横线位置 ("none", 'underline', 'line-through', 'underline line-through')
- textDecorationStyle   线的风格("solid", 'double', 'dotted', 'dashed')
- textDecorationColor  线的颜色
- writingDirection  文本方向("auto", 'ltr', 'rtl')


##需要注意的：
- 如果是texview嵌套，如果里面的texview不重写外层的texview的属性就会把父类的texview的属性样式继承来。


##texview属性实例




```css

import React, {
    AppRegistry,
    Component,
    StyleSheet,
    Text,
    View
} from 'react-native';

class AwesomeProject extends Component {
    render() {
        return (
            <View>
                <Text style={{color:'red'}}>我是红色</Text>
                <Text style={{color:'green',fontSize:20}}>绿色和字体大小。</Text>
                <Text style={{color:'green',fontFamily:'Cochin'}}>绿色和字体名称。</Text>
                <Text style={{color:'pink',fontWeight:'bold'}}> 粉色和加粗。</Text>
                <Text style={{color:'gray',fontStyle:'italic'}}>灰色和斜体。</Text>
                <Text style={{textAlign:'center',fontStyle:'italic'}}>居中和斜体。</Text>
                <Text numberOfLines={1} style={{textAlign:'center',fontStyle:'italic'}}>测试行数My Text Six 居中和斜体。My Text Six 居中和斜体。 My Text Six 居中和斜体。</Text>
                <Text style={{marginLeft:50,marginTop:50,textAlign:'center',fontStyle:'italic'}}>设置文本的间距,居左，居顶部50</Text>
                <Text numberOfLines={2} style={{lineHeight:50,textAlign:'center',fontStyle:'italic'}}>
                    测试行高 测试行高 测试行高 测试行高 测试行高 测试行高 测试行高 测试行高 测试行高 测试行高 测试行高
                    测试行高 测试行高 测试行高 测试行高 测试行高 测试行高
                </Text>
            </View>
        );
    }
}

const styles = StyleSheet.create({
    container: {
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: '#F5FCFF',
        fontWeight: "bold",
        textShadowRadius: 50,
        textShadowColor: "red",
        fontSize: 28,
    },
    instructions: {
        textAlign: 'center',
        color: '#333333',
    },
});

AppRegistry.registerComponent('AwesomeProject', () => AwesomeProject);

```
