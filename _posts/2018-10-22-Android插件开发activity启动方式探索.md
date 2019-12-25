---
layout:     post
title:      "Android插件开发activity启动方式（占坑模式理解）"
subtitle:   ""
date:       2018-10-22 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    -
---


#### Android插件开发activity启动方式（占坑模式理解）

- 插件开发网上文章已经很多了，目前通过不安装apk运行另一个本地的apk的activity界面的方式有两种，
- 通过一个空壳Activity作为代理（Proxy）
- Hook一些系统类

这篇文章主要理解下第一种（占坑）也就是空壳这种方式


#### 占坑模式需要解决问题

- 插件生命周期（完全依赖宿主的代理activity来进行回调）
- 插件资源（通过hook AssetManager 的addAssetPath方法来进行获取）
- 如何加载插件的activity（通过DexClassLoader可以加载一个本地的apk，来获取）

接下来具体的实现

#### 解析本地插件apk

```java
public class PluginManager {
    static class PluginMgrHolder {
        static PluginManager sManager = new PluginManager();
    }

    private static Context mContext;

    Map<String, PluginApk> sMap = new HashMap<>();

    public static PluginManager getInstance() {
        return PluginMgrHolder.sManager;
    }
    public PluginApk getPluginApk(String packageName) {
        return sMap.get(packageName);
    }

    public static void init(Context context) {
        mContext = context.getApplicationContext();
    }

    public final void loadApk(String apkPath) {
        PackageInfo packageInfo = queryPackageInfo(apkPath);
        if (packageInfo == null || TextUtils.isEmpty(packageInfo.packageName)) {
            return;
        }
        // check cache
        PluginApk pluginApk = sMap.get(packageInfo.packageName);

        if (pluginApk == null) {
            pluginApk = createApk(apkPath);
            if (pluginApk != null) {
                pluginApk.packageInfo = packageInfo;
                sMap.put(packageInfo.packageName, pluginApk);
            } else {
                throw new NullPointerException("PluginApk is null");
            }
        }
    }

    private PluginApk createApk(String apkPath) {
        String addAssetPathMethod = "addAssetPath";
        PluginApk pluginApk = null;
        try {
            AssetManager assetManager = AssetManager.class.newInstance();
            Method addAssetPath = assetManager.getClass().getMethod(addAssetPathMethod, String.class);
            addAssetPath.invoke(assetManager, apkPath);
            Resources pluginRes = new Resources(assetManager,
                    mContext.getResources().getDisplayMetrics(),
                    mContext.getResources().getConfiguration());
            pluginApk = new PluginApk(pluginRes);
            pluginApk.classLoader = createDexClassLoader(apkPath);
        } catch (IllegalAccessException
                | InstantiationException
                | NoSuchMethodException
                | InvocationTargetException e) {
            e.printStackTrace();
        }
        return pluginApk;
    }
    private PackageInfo queryPackageInfo(String apkPath) {
        PackageInfo packageInfo = mContext.getPackageManager().getPackageArchiveInfo(apkPath,
                PackageManager.GET_ACTIVITIES | PackageManager.GET_SERVICES);
        if (packageInfo == null) {
            return null;
        }
        return packageInfo;
    }

    private DexClassLoader createDexClassLoader(String apkPath) {
        File dexOutputDir = mContext.getDir("dex", Context.MODE_PRIVATE);
        DexClassLoader loader = new DexClassLoader(apkPath, dexOutputDir.getAbsolutePath(),
                null, mContext.getClassLoader());
        return loader;
    }

    public void startActivity(Intent intent) {
        Intent pluginIntent = new Intent(mContext, ProxyActivity.class);
        Bundle extra = intent.getExtras();
        // complicate if statement
        if (extra == null || !extra.containsKey(Constants.PLUGIN_CLASS_NAME) && !extra.containsKey(Constants.PACKAGE_NAME)) {
            try {
                throw new IllegalAccessException("lack class of plugin and package name");
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
        pluginIntent.putExtras(intent);
        pluginIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        mContext.startActivity(pluginIntent);
    }
}

```


- 可以看到首先通过PackageManager获取插件apk的信息然后保存


#### 解析插件资源

```java
 private PluginApk createApk(String apkPath) {
        String addAssetPathMethod = "addAssetPath";
        PluginApk pluginApk = null;
        try {
            AssetManager assetManager = AssetManager.class.newInstance();
            Method addAssetPath = assetManager.getClass().getMethod(addAssetPathMethod, String.class);
            addAssetPath.invoke(assetManager, apkPath);
            Resources pluginRes = new Resources(assetManager,
                    mContext.getResources().getDisplayMetrics(),
                    mContext.getResources().getConfiguration());
            pluginApk = new PluginApk(pluginRes);
            pluginApk.classLoader = createDexClassLoader(apkPath);
        } catch (IllegalAccessException
                | InstantiationException
                | NoSuchMethodException
                | InvocationTargetException e) {
            e.printStackTrace();
        }
        return pluginApk;
    }


 private DexClassLoader createDexClassLoader(String apkPath) {
        File dexOutputDir = mContext.getDir("dex", Context.MODE_PRIVATE);
        DexClassLoader loader = new DexClassLoader(apkPath, dexOutputDir.getAbsolutePath(),
                null, mContext.getClassLoader());
        return loader;
    }



```

可以看到通过反射AssetManager的addAssetPath方法获取插件的资源，并且获取加载插件类加载器

当我们点击跳转启动插件app的时候

```java
 public final static String PLUGIN_NAME = "plugin.apk";
    public final static String PLUGIN_PACKAGE_NAME = "top.vimerzhao.imageloader";
    public final static String PLUGIN_CLAZZ_NAME = "top.vimerzhao.imageloader.MainActivity";
    PluginManager mPluginManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        findViewById(R.id.tv_content).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                Intent intent = new Intent();
                intent.putExtra(Constants.PACKAGE_NAME, PLUGIN_PACKAGE_NAME);
                intent.putExtra(Constants.PLUGIN_CLASS_NAME, PLUGIN_CLAZZ_NAME);
                mPluginManager.startActivity(intent);
            }
        });
```
由于是个demo项目可以看到插件的类名，和包名已经写死，真实情况这都是动态获取的，打开代理的activity就是壳ProxyActivity，插件的activity的生命周期都是由当前代理类的生命周期来完成，ProxyActivity的逻辑就是ProxyActivity的生命周期回调到LifeCircleController在回调到插件的activity从而实现一个activity的生命周期回调，我们看看LifeCircleController类


```java

public class LifeCircleController implements Pluginable {
    Activity mProxy;
    PluginActivity mPlugin;
    Resources mResources;
    Resources.Theme mTheme;
    PluginApk mPluginApk;
    String mPluginClazz;

    public LifeCircleController(Activity activity) {
        mProxy = activity;
    }

    public void onCreate(Bundle bundle) {
        mPluginClazz = bundle.getString(Constants.PLUGIN_CLASS_NAME);
        String packageName = bundle.getString(Constants.PACKAGE_NAME);
        mPluginApk = PluginManager.getInstance().getPluginApk(packageName);
        try {
            mPlugin = (PluginActivity) loadPluginable(mPluginApk.classLoader, mPluginClazz);
            mPlugin.attach(mProxy, mPluginApk);
            mResources = mPluginApk.pluginRes;
            mPlugin.onCreate(bundle);
        } catch (Exception e) {
            VLog.log("Fail in LifeCircleController onCreate");
            VLog.log(e.getMessage());
            e.printStackTrace();
        }

    }
    private Object loadPluginable(ClassLoader classLoader, String pluginActivityClass)
            throws Exception {
        Class<?> pluginClz = classLoader.loadClass(pluginActivityClass);
        Constructor<?> constructor = pluginClz.getConstructor(new Class[] {});
        constructor.setAccessible(true);
        return constructor.newInstance(new Object[] {});
    }

    @Override
    public void onStart() {
        if (mPlugin != null) {
            mPlugin.onStart();
        }
    }

    @Override
    public void onResume() {
        if (mPlugin != null) {
            mPlugin.onResume();
        }
    }

    @Override
    public void onStop() {
        mPlugin.onStop();
    }

    @Override
    public void onPause() {
        mPlugin.onPause();
    }

    @Override
    public void onDestroy() {
        mPlugin.onDestroy();
    }

    public Resources getResources() {
        return mResources;
    }

    public Resources.Theme getTheme() {
        return mTheme;
    }

    public AssetManager getAssets() {
        return mResources.getAssets();
    }

}

```

可以看到就是通过我们传递过来的插件的类名和包名在通过前面保存的插件apk的dex类加载器，来初始化插件的activity的实例，在调用插件的生命周期方法.


总结：
- 这种方式其实就是把插件的activity看成一个普通的java类，他不具有actyivitiy的所有属性，需要代理类来主动回调他来模拟完成一个activity的正常流程
- 由于一个插件类，就需要在宿主中新开一个activity,就会造成宿主需要提前占很多的activity坑位

参考此文章：https://www.jianshu.com/p/3260baad0979














