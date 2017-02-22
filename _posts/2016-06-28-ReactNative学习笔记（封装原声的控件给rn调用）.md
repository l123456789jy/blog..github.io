---
layout:     post
title:      "ReactNative学习笔记（封装原声的控件给rn调用）"
subtitle:   ""
date:       2016-06-28 15:32:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
---



- 1.我们自己起一个classe为MyToastModule的类并且集成于ReactContextBaseJavaModule这个类实现其内部的三个方法


```java

public class MyToastModule extends ReactContextBaseJavaModule {
  private static final String DURATION_SHORT_KEY = "SHORT";
  private static final String DURATION_LONG_KEY = "LONG";


  public MyToastModule(ReactApplicationContext reactContext) {
    super(reactContext);
  }


  //我们必须实现这个方法这个是用于在js端来根据返回的名字来进行调用（React.NativeModules.ToastAndroid） "MyToastModule"就是我们在rn那边调用的名字，我们就只可以这样调用   *  MyToastModule.show("封装原声Toast",MyToastModule.SHORT);*
  @Override public String getName() {
    return "MyToastModule";
  }


  //这个不是必须实现的方法,返回了需要导出给JavaScript使用的常量
  @Nullable @Override public Map<String, Object> getConstants() {
    final Map<String, Object> constants = new HashMap<>();
    constants.put(DURATION_SHORT_KEY, Toast.LENGTH_SHORT);
    constants.put(DURATION_LONG_KEY, Toast.LENGTH_LONG);
    return constants;
  }


  /**
   * 这个方法是定义给rn调用的，React Native的跨语言访问是异步进行的，
   * 所以想要给JavaScript返回一个值的唯一办法是使用这个回调函数或者发送事件
   */这个就是封装的方法名字，来调用
  @ReactMethod public void show(String message, int duration) {

    Toast.makeText(getReactApplicationContext(), message, duration).show();
  }
}


```





- 第2步我们需实现ReactPackage我们需要在应用的Package类的createNativeModules方法中添加这个模块。


```java
public class AnExampleReactPackage implements ReactPackage {

  @Override
  public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }


  @Override public List<Class<? extends JavaScriptModule>> createJSModules() {
    return Collections.emptyList();
  }


  /**
   * 在这个方法中返回自己封装的toast 这里存放的就是自己刚才创建的MyToastModule，注意这里的泛型是NativeModule
   * @param reactContext
   * @return
   */
  @Override
  public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();
    modules.add(new MyToastModule(reactContext));
    return modules;
  }
}

```




- 第3部我们需要在MainActivity的中注册我们自己定义的toastpackage




```java
public class MainActivity extends ReactActivity {

  /**
   * Returns the name of the main component registered from JavaScript.
   * This is used to schedule rendering of the component.
   */
  @Override protected String getMainComponentName() {
    return "RnDemo";
  }


  /**
   * Returns whether dev mode should be enabled.
   * This enables e.g. the dev menu.
   */
  @Override protected boolean getUseDeveloperSupport() {
    return BuildConfig.DEBUG;
  }


  /**
   * A list of packages used by the app. If the app uses additional views
   * or modules besides the default ones, add more packages here.
   */
  @Override protected List<ReactPackage> getPackages() {
    return Arrays.<ReactPackage>asList(new MainReactPackage(),
      new AnExampleReactPackage());//在这里加上我自己注册的package
  }
}


```




- 最后一步我们在js端调用


```js
/**
 * Sample React Native App
 * https://github.com/facebook/react-native
 */

import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  NativeModules, 需要引入这个模块
  View
} from 'react-native';

var MyToastModule= NativeModules.MyToastModule;
//这里一定要调用
MyToastModule.show("封装原声Toast",MyToastModule.SHORT);
class RnDemo extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          Welcome to rn show
        </Text>
        <Text style={styles.instructions}>
          To get started, edit index.android.js
        </Text>
        <Text style={styles.instructions}>
          Shake or press menu button for dev menu
        </Text>

      </View>

    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  welcome: {
    fontSize: 20,
    textAlign: 'center',
    margin: 10,
  },
  instructions: {
    textAlign: 'center',
    color: '#333333',
    marginBottom: 5,
  },
});

AppRegistry.registerComponent('RnDemo', () => RnDemo);


```
