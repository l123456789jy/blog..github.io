---
layout:     post
title:      "Android 9 GPS JNI 到HAL 层分析"
subtitle:   ""
date:       2022-03-02 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags: Android
    -
---
- JNI层调用在 frameworks\base\services\core\jni\com_android_server_location_GnssLocationProvider.cpp

- 我们跟着 class_init_native 方法看初始化

```
#目录位置 frameworks\base\services\core\jni\com_android_server_location_GnssLocationProvider.cpp
static void android_location_GnssLocationProvider_class_init_native(JNIEnv* env, jclass clazz) {
    gnssHal_V1_1 = IGnss_V1_1::getService();
    if (gnssHal_V1_1 == nullptr) {
        ALOGD("gnssHal 1.1 was null, trying 1.0");
        gnssHal = IGnss_V1_0::getService();
    } else {
        gnssHal = gnssHal_V1_1;
    }
}
```
在初始化中我们看到这里进行了GPS 的HADL层的初始化，使用的GNSS版本是 android/hardware/gnss/1.0/IGnss.h  1.0版本，接下来看 native_start 开始定位的方法



```
static jboolean android_location_GnssLocationProvider_start(JNIEnv* /* env */, jobject /* obj */) {
    if (gnssHal != nullptr) {
       #这里调用了HADL层的start开始定位方法
        auto result = gnssHal->start();
        if (!result.isOk()) {
            return JNI_FALSE;
        } else {
            return result;
        }
    } else {
        return JNI_FALSE;
    }
}
```

接下来，我们进入到GNSS的HADL层代码位置在 hardware\interfaces\gnss\1.0，这里的 IGnss.hal是定义HIDL服务的方法名字，具体的实现是在default目录下 Gnss.cpp 文件，我们看这里的start方法做了什么




```
IGnss* HIDL_FETCH_IGnss(const char* /* hal */) {
    hw_module_t* module;
    IGnss* iface = nullptr;
    #调用了HAL层获取厂商的so操作拿到操作GPS的对象
    int err = hw_get_module(GPS_HARDWARE_MODULE_ID, (hw_module_t const**)&module);

    if (err == 0) {
        hw_device_t* device;
        err = module->methods->open(module, GPS_HARDWARE_MODULE_ID, &device);
        if (err == 0) {
          #得到GPS对象进行操作
            iface = new Gnss(reinterpret_cast<gps_device_t*>(device));
        } else {
            ALOGE("gnssDevice open %s failed: %d", GPS_HARDWARE_MODULE_ID, err);
        }
    } else {
      ALOGE("gnss hw_get_module %s failed: %d", GPS_HARDWARE_MODULE_ID, err);
    }
    return iface;
}
```
调用了 mGnssIface的start方法就是 gps_device_t 定位开始的方法



```
Return<bool> Gnss::start()  {
    if (mGnssIface == nullptr) {
        ALOGE("%s: Gnss interface is unavailable", __func__);
        return false;
    }

    return (mGnssIface->start() == 0);
}
```

![image.png](https://upload-images.jianshu.io/upload_images/1205414-c8d7c62174af625d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到此全部从应用层到底层全部走通

      