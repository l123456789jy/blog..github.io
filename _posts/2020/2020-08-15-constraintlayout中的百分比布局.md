---
layout:     post
title:      "constraintlayout中的百分比布局"
subtitle:   ""
date:       2020-08-15 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    -
---




#### constraintlayout布局中为我们提供了，两种百分布局可以帮助我们去适配界面


#### 第一种

    layout_constraintVertical_bias    该属性代表的意思是垂直距离的百分比，默认0.5就是剧中


#### 第二种


    layout_constraintHorizontal_bias  水平距离的百分比，也是一样默认0.5就是剧中


如果要使用以上属性，必须要设置该view的上下左右的约束

```xml
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/tv__id_item_layout"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:background="@color/colorAccent"
        android:textAllCaps="false"
        android:textColor="@android:color/white"
        android:textSize="20sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/ic_launcher_background"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_bias="0.9"
        app:layout_constraintLeft_toLeftOf="@id/tv__id_item_layout"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="@id/tv__id_item_layout"
        app:layout_constraintVertical_bias="0.8"></ImageView>


</androidx.constraintlayout.widget.ConstraintLayout>
```

##### 看看各大屏幕的效果


![{94CE0FE4-6926-451F-949A-782E275ED40D}_20200815102622](//tva1.sinaimg.cn/large/9f723435gy1ghrae3fb87j20zm0fegmc.jpg)


##### 还有一种

    layout_constraintDimensionRatio 宽高百分比和高宽百分比的约束属性


要想使用此属性，必须设置0db,也就是设置`MATCH_CONSTRAINT`属性，才能生效，这里默认的是宽和高的比值，也可以自己定义 H 3:1 这样格式就是高比宽

![{0C8E0F90-5214-4480-A1F5-348A0306DDC9}_20200815104622](//tvax4.sinaimg.cn/large/9f723435gy1ghraxekqelj20sy0h4757.jpg)


```xml
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/tv__id_item_layout"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:background="@color/colorAccent"
        android:textAllCaps="false"
        android:textColor="@android:color/white"
        android:textSize="20sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <ImageView
        app:layout_constraintDimensionRatio="1:5"
        android:layout_width="wrap_content"
        android:layout_height="0dp"
        android:background="@drawable/ic_launcher_background"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="@id/tv__id_item_layout"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="@id/tv__id_item_layout"></ImageView>


</androidx.constraintlayout.widget.ConstraintLayout>
```

