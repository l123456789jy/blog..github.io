---
layout:     post
title:      "ReactNative实现DrawerLayoutAndroid点击侧滑栏展开和关闭"
subtitle:   "Welcome to my blog "
date:       2016-07-04 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - DrawerLayoutAndroid
---






##废话不多说看代码


```java
import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Image,
  ListView,
  ToastAndroid,
  ToolbarAndroid,
  DrawerLayoutAndroid,
  Dimensions,
} from 'react-native';

var page = 1;
var REQUEST_URL = 'http://gank.io/api/data/Android/10/' + page;


//引入欢迎界面
var SplashScreen = require('./js/SplashScreen');
//引入返回图标
var back_bg = require('./img/back.png');
//存放返回的数据的数组
var movieData = new Array();


class RnDemo extends Component {
  constructor(props) {
    super(props);
    this.state = {
      dataSource: new ListView.DataSource({
        rowHasChanged: (row1, row2) => row1 !== row2,
      }),
      loaded: false,
    };
  }

  //界面开始加载获取数据
  componentDidMount() {
    this.fetchData(REQUEST_URL);
  }


  //接受请求成功的回调的结果
  onResoutData(responseData) {
    var concat = movieData.concat(responseData.results);
    movieData = concat;
    this.setState({
      dataSource: this.state.dataSource.cloneWithRows(movieData),
      loaded: true,
    });
  }


  render() {
    //如果是第一打开加载就先打开欢迎界面
    if (!this.state.loaded) {
      return (
        <SplashScreen />
      );

    }

    //初始化侧边栏
    return this.renderDrawableView();

  }

  //打开侧滑栏
  onPenLeftDrawable() {
    this.drawer.openDrawer();
  }

  //加载网络数据
  fetchData(REQUEST_URL) {
    fetch(REQUEST_URL)
      .then((response) => response.json())
      .then((responseData) => {
        this.onResoutData(responseData)
      })
  }

  //监听滑动到底部
  loadmore() {
    page++;
    var REQUEST_URL = 'http://gank.io/api/data/Android/10/' + page;
    this.fetchData(REQUEST_URL);

  }


  //返回侧滑栏
  renderDrawableView() {
    //侧滑列表显示的布局
    var navigationView = (
      <View style={{flex: 1, backgroundColor: 'blue'}}>
        <Text style={{margin: 10,color:'#fff',fontSize: 15, textAlign: 'center'}}>我是导航功能栏标题</Text>
        <Text style={{marginTop: 10,marginLeft:20,color:'#fff',fontSize: 15, textAlign: 'left'}}>1.功能1</Text>
        <Text style={{marginTop: 10,marginLeft:20,color:'#fff',fontSize: 15, textAlign: 'left'}}>2.功能2</Text>
      </View>
    );

    return (
      <DrawerLayoutAndroid
        ref={(drawer) => { this.drawer = drawer; }}
        drawerWidth={Dimensions.get('window').width / 5 * 3}
        drawerPosition={DrawerLayoutAndroid.positions.left}
        //这个是加载侧边划出的布局
        renderNavigationView={() => navigationView}
      >

        <View style={styles.container2}>
          <ToolbarAndroid   //标题栏
            navIcon={back_bg}
            onIconClicked={this.onPenLeftDrawable.bind(this)} //左上角打开侧划栏点击方法
            titleColor='#ffffff'  //只支持RGB数值，设置标题的字体颜色
            style={styles.toolbar}
            title="Android资源列表"></ToolbarAndroid>
          <ListView
            initialListSize={1}
            onEndReachedThreshold={10}
            dataSource={this.state.dataSource}
            renderRow={this.renderMovie}
            onEndReached={this.loadmore.bind(this)}
            style={styles.listviewstyle}
          />
        </View>
      </DrawerLayoutAndroid>
    );
  }


  //显示干活数据的具体逻辑
  renderMovie(results) {
    return (
      <View style={styles.container}>
        <Image
          source={{uri: results.url}}
          style={styles.thumbnail}
        />
        <View style={styles.rightContainer}>
          <Text style={styles.title}>{results.desc}</Text>
          <Text style={styles.year}>{results.createdAt}</Text>
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({

  toolbar: {
    backgroundColor: 'blue',
    height: 56,

  },

  container2: {
    flex: 1,
    flexDirection: 'column', //竖直按顺序从上往下排列
  },

  container: {
    flexDirection: 'row', //按顺序从左往右排列
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  thumbnail: {
    width: 53,
    height: 81,
  },
  rightContainer: {
    flex: 1,
  },
  title: {
    fontSize: 20,
    marginBottom: 8,
    textAlign: 'center',
  },
  year: {
    textAlign: 'center',
  },
  listviewstyle: {
    paddingTop: 20,
    backgroundColor: '#F5FCFF',

  },
});
AppRegistry.registerComponent('RnDemo', () => RnDemo);


```





![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1205414-b74d20ec730771c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##这里我遇到的疑问就是：

     ref={(drawer) => { this.drawer = drawer; }}，这个什么意思？ref哪来的？



##现在我就来回答：

- 这句话的意思就是将drawer的对象赋值给drawer
- ref是什么你可以简单的理解为是一个关键字,用它可以保存任何的方法和取值


[ref参考链接](http://wiki.jikexueyuan.com/project/react/more-about-refs.html)


Demo地址：https://github.com/l123456789jy/RnDemo
