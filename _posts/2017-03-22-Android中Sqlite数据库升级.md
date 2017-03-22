---
layout:     post
title:      "Android中Sqlite数据库升级"
subtitle:   ""
date:       2017-03-22 15:32:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
---



1.数据库升级的意义

我们在开发Android应用的时候，不可避免地要使用数据库。而数据库的结构在第一版的时候定下来，之后发布功能更新，或增加业务逻辑，原来的数据库结构可能就不适用了。而如果数据库的结构与之前版本的结构不同，新版本的应用读取旧数据库肯定会出问题。

2.解决办法只有两种：
- .让用户卸载老版本再安装新的程序；
- .软件自行更新数据库结构。


3.我们在Android中使用的数据库一般都是Sqlite，这里就按照Sqlite来处理.

```java
public class KgcDatabaseOpenHelper extends SQLiteOpenHelper {
    public static final String NAME = "kgc.db";
    public static final int VERSION = 1;

    public KgcDatabaseOpenHelper(Context context) {
        super(context, NAME, null, VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        JLog.e("KgcDatabaseOpenHelper============onCreate");

        createTable(db);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        JLog.e("KgcDatabaseOpenHelper============oldVersion="+oldVersion);
        JLog.e("KgcDatabaseOpenHelper============newVersion="+newVersion);

        switch (oldVersion){
            case 2:
                break;
        }
    }

```
这里就需要了解 onCreate和onUpgrade分别在什么时候执行

4 onCreate 只会执行一次
           比如你第一次的VERSION 是1后来新用户安装你最近的APK安装包，你的数据版本     VERSION 直接升级为2，因为用户之前没有安装软件，所以只会走onCreate ,如果用户之前安装过就会走onUpgrade的方法



5.所以我们新的数据库逻辑也要在onCreate方法中执行，也要在onUpgrade中执行，确保老用户和新用户都能使用，

6.如果只是增加一张表，那么直接在onCreate和onUpgrade中执行创建表的逻辑就行。


7.如果修改已有表：


-     将现有表重命名为临时表
-       创建新表
-      将临时表的数据导入新表（注意处理修改的列）
-      删除临时表。





```java

 public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        if (oldVersion==2){
            db.execSQL("ALTER TABLE A RENAME TO A_temp");
            db.execSQL("CREATE TABLE A(_id integer primary key"
                 + "autoincrement, region varchar, code varchar, "
                 + "country varchar)");
            db.execSQL("insert into A(_id, region, code, country) "
                + "select _id, region, code, \"CHINA\" from A_temp");
            db.execSQL("DROP TABLE A_temp");
        }
    }

```
这样就是升级现有表结构方法