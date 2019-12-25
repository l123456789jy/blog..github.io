---
layout:     post
title:      "Navigator组件踩坑"
subtitle:   "Welcome to my blog "
date:       2016-07-08 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - Navigator
---





##首先我们要知道Navigator是什么？

        Navigator他是一个界面的管理器，也可以说是路由，做过安卓的都知道，它就相当于我们的Activity的栈，来存放应用的界面。



##需要注意的是Navigator一定要在初始界面进行初始化，要不然在其他界面获取不到对象。


##看下他的方法：


- initialRoute: 初始路由，也就是我们需要在navigator栈底放置的路由，因为此时也是栈顶，因此也就是初始化整个App的首页。

- configureScene: 设置页面切换动画，具体的值大家可以在node_modules/react-native/Libraries/CustomComponents/Navigator/NavigatorSceneConfigs.js 源库中找到。或者直接在sublime Text 3 中使用命令command+p 命令，然后输入NavigatorSceneConfigs进行文件搜索。笔者看了一下，大概有不到十种动画，这里就不做具体介绍了。

- renderScene: 渲染当前路由场景，也就是渲染栈顶路由。回调方法里一个参数是当前路由route，

- 另一个是navigator 路由栈。这里需要细讲一下：
route: 路由里包含一个必要的component变量和一些可选的参数，而component则就是当前需要渲染的组件。其他的一些可选的参数也是以键值对的方式保存在route对象中，并在这里以属性的方式传递给对应的component组件中。

- navigator: 这是Navigator组件在本回调方法中的参数，也就是我们在前边提到的路由栈。会在每次渲染新route的时候以属性的形式传递给route中component的对象。它可以在对应的场景中做一些操作，这里我们给出官方的部分文档：
getCurrentRoutes() - 获取当前栈里的路由，也就是push进来，没有pop掉的那些。
jumpBack() - 跳回之前的路由，当然前提是保留现在的，还可以再跳回来，会给你保留原样。
jumpForward() - 上一个方法不是调到之前的路由了么，用这个跳回来就好了。
jumpTo(route) - 跳转到已有的场景并且不卸载。
push(route) - 跳转到新的场景，并且将场景入栈，你可以稍后跳转过去
pop() - 跳转回去并且卸载掉当前场景
replace(route) - 用一个新的路由替换掉当前场景
replaceAtIndex(route, index) - 替换掉指定序列的路由场景
replacePrevious(route) - 替换掉之前的场景
immediatelyResetRouteStack(routeStack) - 用新的路由数组来重置路由栈
popToRoute(route) - pop到路由指定的场景，其他的场景将会卸载。
popToTop() - pop到栈中的第一个场景，卸载掉所有的其他场景。
{...route} 这是一句 es6 语法，表示将route中所有的键值对以属性赋值的方式展开，也就是{name: 'pober', gender: 'man'} 到 name = 'pober' gender = 'man'之间的转换。这样就把route对象中所有的键值对就很容易地放到route.component组件的props属性中去啦～因此，这个语法在自定义控件的时候也经常用到，主要用来兼容组件中其他所有的属性，如自定义ListView兼容官方ListView的所有属性。
passProps 这是一个我们自己定义的key，后边会提到。同样是可选的


    原文链接：http://www.jianshu.com/p/03ddca8f1982






##废话不多说我们直接上主要代码




```java

import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Navigator,
} from 'react-native';

//引入主界面
var Home = require('./Home');
//相当于application,Navigatory一定要在应用启动的时候初始化
class RnDemo extends Component {
  constructor (props) {
    super(props)
  }

  render () {
    return (
      <Navigator style = {styles.container}
                 initialRoute={{
          component: Home   //将根界面压栈
        }}
                 renderScene={(route, navigator) => { // 用来渲染navigator栈顶的route里的component页面
          return <route.component navigator={navigator} {...route} {...route.passProps}/>// {...route.passProps}即就是把passProps里的键值对全部以给属性赋值的方式展开 如：test={10}
        }}/>
    )
  }

}

const styles = StyleSheet.create({
  container: {
    flex: 1
  }
});
AppRegistry.registerComponent('RnDemo', () => RnDemo);


```





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
  TouchableOpacity,
  InteractionManager,
} from 'react-native';

var page = 1;
var REQUEST_URL = 'http://gank.io/api/data/Android/10/' + page;


//引入欢迎界面
var SplashScreen = require('./js/SplashScreen');
//引入关于我的界面
var AboutMe = require('./js/AboutMe');


//引入返回图标
var back_bg = require('./img/menu.png');
//侧滑栏顶部的背景
var drawable_bg = require('./img/drawerlayout.png');


//存放返回的数据的数组
var movieData = new Array();

//主界面
class Home extends Component {
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
      <View style={styles.drawableContainer}>

        <View style={styles.drawableHeard}>

          <Image
            style={styles.drawableHeard}
            source={drawable_bg}
          />

          <Text style={styles.drawableHeardItem1}>
            Lazy
          </Text>

          <Text style={styles.drawableHeardItem1}>
            让生活更精彩
          </Text>

        </View>

        <TouchableOpacity onPress={this.onPressDraweraboutMeItem.bind(this)}>
        <View style={styles.drawableContainer2}>
          <Text style={styles.drawableHeardItem2}>关于我</Text>
        </View>
        </TouchableOpacity>


        <TouchableOpacity onPress={this.onPressDrawerCloseItem.bind(this)}>
        <View style={styles.drawableContainer2}>
        <Text style={styles.drawableHeardItem2}>关闭</Text>
        </View>
        </TouchableOpacity>
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

  //关于我
  onPressDraweraboutMeItem(){
    this.drawer.closeDrawer();
    //延长在执行回调方法，提高体验,打开关于我的界面，并且将我的界面压栈
    InteractionManager.runAfterInteractions(() => {
      this.props.navigator.push({
        component: AboutMe,
      });
    });

  }


  //关闭侧滑栏
  onPressDrawerCloseItem(){
    this.drawer.closeDrawer();
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


  drawableContainer2: {
    marginTop:5,
    height: 30,
    backgroundColor: '#838383'
  },


  drawableHeardItem2: {
    color: '#fcfcfc',
    fontSize: 15,
    marginLeft: 10,
    marginTop:5,
  },


  drawableContainer: {
    flex: 1,
    backgroundColor: '#fcfcfc'
  },



  drawableHeard: {
    width: Dimensions.get('window').width / 5 * 3,
    height: 120,
    justifyContent: 'flex-end',  //与父组件的底部对齐。
    paddingBottom: 10,
    backgroundColor: '#3e9ce9'
  },



  drawableHeardItem1: {
    fontSize: 20,
    textAlign: 'left',  //靠左
    color: '#fcfcfc',
    marginLeft: 10,
  },



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

//暴漏给其他模块调用
module.exports = Home;


```


注释我已经写的很清楚了，完整的代码：https://github.com/l123456789jy/RnDemo
