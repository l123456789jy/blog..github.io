---
layout:     post
title:      "Constraintlayout布局基本使用"
subtitle:   ""
date:       2020-07-05 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - Constraintlayout布局基本使用
---




- Constraintlayout基本使用文档：https://developer.android.com/training/constraint-layout?hl=zh-cn
- Constraintlayout是为了减少布局的嵌套，避免我们使用多级布局嵌套，但它并不能彻底解决布局的嵌套问题，但是确实可以让我们更少的使用相对和线性布局

- 如果我们之前有老版本的布局想要转化成Constraintlayout布局，只需要这样做即可


![layout-editor-convert-to-constraint_2x](https://tvax4.sinaimg.cn/large/9f723435gy1ggfwke6904j20n20i5q38.jpg)

在自己的父布局上执行转换即可


- 剩下基本操作就可以看官方文档就可以了

- 文档上并没有说，我们怎么使用嵌套背景的情况下使用约束布局，比如我一个卡片CardView里面要嵌套View

![微信截图_20200705105728](https://tva1.sinaimg.cn/large/9f723435gy1ggfwu7b5emj20gr0hhaay.jpg)


- 这样布局其实就避免不了嵌套了

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#efefef">

    <androidx.cardview.widget.CardView
        android:id="@+id/cardView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="15dp"
        android:layout_marginTop="15dp"
        android:layout_marginEnd="15dp"
        android:layout_marginBottom="371dp"
        android:background="@android:color/white"
        android:elevation="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        card_view:cardCornerRadius="15dp"
        tools:ignore="MissingConstraints">

        <androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="219dp">


            <ImageView
                android:id="@+id/imageView3"
                android:layout_width="150dp"
                android:layout_height="150dp"
                android:layout_marginStart="32dp"
                android:layout_marginTop="24dp"
                android:layout_marginEnd="28dp"
                android:layout_marginBottom="32dp"
                android:background="@drawable/lake"
                android:text="haha"
                app:layout_constraintBottom_toTopOf="@+id/textView"
                app:layout_constraintEnd_toStartOf="@+id/textView7"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                tools:ignore="MissingConstraints"></ImageView>

            <TextView
                android:id="@+id/textView7"
                android:layout_width="75dp"
                android:layout_height="0dp"
                android:layout_marginStart="56dp"
                android:layout_marginTop="40dp"
                android:layout_marginBottom="152dp"
                android:text="我是标题"
                app:layout_constraintBottom_toTopOf="@+id/textView8"
                app:layout_constraintStart_toEndOf="@+id/imageView3"
                app:layout_constraintTop_toTopOf="parent"
                app:layout_constraintVertical_bias="1.0"
                tools:ignore="MissingConstraints">

            </TextView>

            <TextView
                android:id="@+id/textView"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginStart="20dp"
                android:layout_marginTop="28dp"
                android:text="我是文本内容"
                app:layout_constraintStart_toStartOf="@+id/imageView3"
                app:layout_constraintTop_toBottomOf="@+id/imageView3"
                tools:ignore="MissingConstraints">

            </TextView>

            <TextView
                android:id="@+id/textView8"
                android:layout_width="75dp"
                android:layout_height="wrap_content"
                android:layout_marginTop="205dp"
                android:layout_marginEnd="36dp"
                android:text="1990-04-78"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                tools:ignore="MissingConstraints">

            </TextView>
        </androidx.constraintlayout.widget.ConstraintLayout>
    </androidx.cardview.widget.CardView>

</androidx.constraintlayout.widget.ConstraintLayout>
```


- 我们要想在CardView里面接着使用约束布局就父布局就必须是ConstraintLayout，因此还要在加上一层 ConstraintLayout