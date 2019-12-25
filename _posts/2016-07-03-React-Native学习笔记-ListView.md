---
layout:     post
title:      "React-Native学习笔记-ListView"
subtitle:   ""
date:       2016-07-03 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ReactNative
    - ListView
---





- ListView这个是用的最多的核心组件

##基本属性方法
- ScrollView相关属性样式全部继承
- dataSource   ListViewDataSource  设置ListView的数据源
- initialListSize  number  进行设置ListView组件刚刚加载的时候渲染的列表行数，用这个属性确定首屏或者首页加载的数量，而不是花大量的时间渲染加载很多页面数据，提高性能
- onChangeVisibleRows  function  (visibleRows,changedRows)=>void。当可见的行发生变化的时候回调该方法。visibleRows参数对所有可见的行为{selectionID:{rowId:true}}的形式，changedRow参数对已经改变可见的行为{selectionID:{rowID:true|false}}。该值true代表可见，false代表在视图之外不可见的行。
- onEndReachedThreshold  number 当偏移量达到设置的临界值调用onEndReached
- onEndReached function 方法，当所有的数据项行被渲染之后，并且列表往下进行滚动。一直滚动到距离底部onEndReachedThredshold设置的值进行回调该方法。原生的滚动事件进行传递(通过参数的形式)。
- pageSize   number 每一次事件的循环渲染的行数
- removeClippedSubviews  bool  该属性用于提供大数据列表的滚动性能。该使用的时候需要给每一行(row)的布局添加over:'hidden'样式。该属性默认是开启状态。
- renderFooter function 方法  ()=>renderable ，在每次渲染过程中头和尾总会重新进行渲染。如果发现该重新绘制的性能开销比较大的时候，可以使用StaticContainer容器或者其他合适的组件。在每一次渲染过程中Footer(尾)该会一直在列表的底部，header(头)该会一直在列表的头部
- renderHeader  function 方法 使用情况和上面的renderFooter差不多
- renderRow function 方法   (rowData,sectionID,rowID,highlightRow)=>renderable   该方法有四个参数，其中分别为数据源中一条数据，分组的ID，行的ID，以及标记是否是高亮选中的状态信息。
- renderScrollComponent function 方法 (props)=>renderable  该方法可以返回一个可以滚动的组件。默认该会返回一个ScrollView
- renderSectionHeader function (sectionData,sectionID)=>renderable   如果设置了该方法，这样会为每一个section渲染一个粘性的header视图。该视图粘性的效果是当刚刚被渲染开始的时候，该会处于对应的内容的顶部，然后开始滑动的时候，该会跑到屏幕的顶端。直到滑动到下一个section的header(头)视图，然后被替代为止。
- renderSeparator function  (sectionID,rowID,adjacentRowHighlighted)=>renderable 如果设置该方法，会在被每一行的下面渲染一个组件作为分隔。除了每一个section分组的头部视图前面的最后一行。
- scrollRenderAheadDistance number  进行设置当该行进入屏幕多少像素以内之后就开始渲染该行



##实例
```java
'use strict';
import React, {Component} from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  ListView,
  Image,
  TouchableOpacity,
} from 'react-native';
//引入

var THUMB_URLS = [
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
  require('./imgs/send.png'),
];
var RnDemo = React.createClass({
  getInitialState: function() {
    var ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
    return {
      dataSource: ds.cloneWithRows(['row 1', 'row 2','row 3','row 4','row 5','row 6','row 7','row 8','row 9','row 10','row 11','row 12']),
    };
  },
  _renderRow: function(rowData: string, sectionID: number, rowID: number) {
    var imgSource = THUMB_URLS[rowID];
    return (
      <TouchableOpacity>
        <View>
          <View style={styles.row}>
            <Image style={styles.thumb} source={imgSource} />
            <Text style={{flex:1,fontSize:16,color:'blue'}}>
              {rowData + '我是测试行号哦~'}
            </Text>
          </View>
        </View>
      </TouchableOpacity>
    );
  },
  render: function() {
    return (
      <ListView
        dataSource={this.state.dataSource}
        renderRow={this._renderRow}
      />
    );
  }
});
var styles = StyleSheet.create({
  row: {
    flexDirection: 'row',
    justifyContent: 'center',
    padding: 10,
    backgroundColor: '#F6F6F6',
  },
  thumb: {
    width: 50,
    height: 50,
  },
});
AppRegistry.registerComponent('RnDemo', () => RnDemo);


```
