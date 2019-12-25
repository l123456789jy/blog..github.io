---
layout:     post
title:      "Android-Debug-Database无法查看android Romm"
subtitle:   ""
date:       2018-09-03 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Room
---


#### 1.最近数据库切换到`google android Romm`,调试的时候居然不能使用`Android-Debug-Database`查看数据库，一脸懵逼

#### 2.原来是他默认开启了数据库的`WAL模式`，这种模式比默认的模式有什么好呢， 由于使用WAL比ROLLBACK JOURNAL的模式减少了写的I/O，所以写入时速度较快，但是由于在读取数据时也需要读取WAL日志验证数据的正确性，所以读取数据相对要慢。 所以大家也要根据自己应用的场景去使用这种模式。

#### 而`google android Romm`默认是自动默认，根据官网说明，就是当设备低于16的时候会启用`TRUNCATE`默认，高于16就会启用`WRITE_AHEAD_LOGGING`模式

#### 所以治理只需要设置模式为`TRUNCATE`模式即可，因为`WRITE_AHEAD_LOGGING`模式不会立即将数据写入到数据库，所以我们无法查看

```java
@Database(entities = {KgcAssistant.class}, version = 1)
public abstract class KgcAssistantRoomDatabase extends RoomDatabase {

  public abstract KgcAttendanceDao kgcAssistantDao();

  private static KgcAssistantRoomDatabase INSTANCE;

  public static KgcAssistantRoomDatabase getDatabase(final Context context) {
    if (INSTANCE == null) {
      synchronized (KgcAssistantRoomDatabase.class) {
        if (INSTANCE == null) {
          INSTANCE = Room.databaseBuilder(context.getApplicationContext(), KgcAssistantRoomDatabase.class, "kgc_database")
              //设置此种模式才能使用Android-Debug-Database查看数据库,这种模式会造成并发降低
              .setJournalMode(RoomDatabase.JournalMode.TRUNCATE)
              .build();
        }
      }
    }
    return INSTANCE;
  }


}
```