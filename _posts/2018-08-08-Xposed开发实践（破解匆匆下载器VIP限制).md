---
layout:     post
title:      "Xposed开发实践（破解匆匆下载器VIP限制）"
subtitle:   ""
date:       2018-08-08 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 逆向
---


一直没有找到合适的种子下载器，偶尔看到一个`匆匆下载器`，试了下解析成功率相当高，但是是要付费的，我这种穷逼，拿来的钱，所以就打算逆向下，看看

#### 第一步分析
- 打开APKTool,分析改APK，结果发现居然加密，是用梆梆加密的
- 关于如何破解梆梆加密请看我这篇文章[App“梆梆加固”破解](https://www.jianshu.com/p/955c0b1507c7 "App“梆梆加固”破解")

#### 第二部开始分析，关键代码

- 我们把源码dum出来之后，使用 dexjar,打开
![](http://ww1.sinaimg.cn/large/9f723435ly1fu27ud1a7tj20vh0j840s.jpg)

- 源码全部看到啦
- 我们通过`ACTIVITY TOP`获取播放界面的完整类名，和路径
![](http://ww1.sinaimg.cn/large/9f723435ly1fu27x8od2oj20x70inneh.jpg)
这里我么就看到了，他的包名，和当前Activity的完整路径，接下在我们就去找`AdvancedPlayActivity`这个类

- 然后我们根据他的提示语去搜索，找到关键代码
这个是游客登录的限制逻辑
![](http://ww1.sinaimg.cn/large/9f723435ly1fu27zkde0pj20ur0g3mxe.jpg)

这个是vip逻辑的限制

![](http://ww1.sinaimg.cn/large/9f723435ly1fu280imohdj20uu08874d.jpg)

- 有了关键代买，我们就好办了，我们分析，他这个逻辑都是在一个叫做，`startPositionTimer` 方法里面被调用，我们只需要hook住这个方法，替换这个方法，替换成一个空实现不就ok了


```java
public class Man implements IXposedHookLoadPackage {

  private static final String FILTER_PKGNAME = "com.congcong.dl.application";
  private static final String BAI_DU_PKGNAME = "com.congcong.dl.application.widget.BDCloudVideoView";
  private static final String AD_PKGNAME = "com.congcong.dl.application.cc.bar.AdvancedMediaController";
  private static final String LOG_PKGNAME = "android.util.Log";
  @Override
  public void handleLoadPackage(final LoadPackageParam loadPackageParam) throws Throwable {
    Log.e("handleLoadPackage", loadPackageParam.packageName);
    if (FILTER_PKGNAME.equals(loadPackageParam.packageName)) {

      //这里是为了解决app多dex进行hook的问题，Xposed默认是hook主dex
      XposedHelpers
          .findAndHookMethod(Application.class, "attach", Context.class, new XC_MethodHook() {
            @Override
            protected void afterHookedMethod(MethodHookParam param) throws Throwable {
              Log.e("handleLoadPackage", "afterHookedMethod");
              final ClassLoader cl = ((Context) param.args[0]).getClassLoader();
              XposedHelpers.findAndHookMethod("com.congcong.dl.application.cc.AdvancedPlayActivity", cl, "onCreate", Bundle.class, new XC_MethodHook() {
                @Override
                protected void afterHookedMethod(MethodHookParam param) throws Throwable {
                  Log.e("handleLoadPackage", "onCreate");
                  //获取当前hook的activity
                  final Activity thisObject = (Activity) param.thisObject;
                  Uri data = thisObject.getIntent().getData();
                  Log.e("handleLoadPackage",data.toString());
                  final Class<?> aClass = cl.loadClass(AD_PKGNAME);
                  //hook AdvancedMediaController 中 startPositionTimer方法并且替换为空实现
                  XposedHelpers.findAndHookMethod(aClass, "startPositionTimer",
                      new XC_MethodReplacement() {
                        @Override
                        protected Object replaceHookedMethod(MethodHookParam methodHookParam)
                            throws Throwable {
                          Toast.makeText(thisObject,"hook,成功！",Toast.LENGTH_SHORT).show();
                          Log.e("handleLoadPackage", "replaceHookedMethod");
                          return null;
                        }
                      });
                }
              });

            }
          });
    }


  }
}
```
#### 破解下载功能

- 目前还不能使用下载功能这个也是VIP特有功能
- 找到下载的activity名字为`ShowListActivityQ`
- 通过分析，发现他进入这个界面会调用一次登录方法
- 而且发现最关键的是通过一个属性`vip`字段来判断是否是vip的那还不简单，搞起来
- hook登录方法，然后动态修改他的vip属性值为1就可以了

```java
 final Class<?> aClass = cl.loadClass("com.congcong.dl.application.cc.ShowListActivityQ");
              XposedHelpers.findAndHookMethod(aClass, "getlogin",
                  new XC_MethodReplacement() {
                    @Override
                    protected Object replaceHookedMethod(MethodHookParam methodHookParam)
                        throws Throwable {
                      Toast.makeText(((Activity) methodHookParam.thisObject),"hook,成功！",Toast.LENGTH_SHORT).show();
                      //通过查看源码发现他标记用户的vip标识是vip=1是0不是，所以动态修改他的属性就行了
                      XposedHelpers.setObjectField(methodHookParam.thisObject,"vip","1");
                      Log.e("handleLoadPackage", "replaceHookedMethod");
                      return null;
                    }
                  });

            }
```




这样就可以无限制的看了，代码已经上传github[地址](https://github.com/l123456789jy/GradleTask "地址"),在xposted 里面搜索匆匆就可以使用该插件了，已经升级2.0可以使用下载功能了