---
layout:     post
title:      "Android插件开发activity启动方式（hook模式理解）"
subtitle:   ""
date:       2018-10-24 16:42:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:
tags:
    -
---


#### Android插件开发activity启动方式（hook模式理解）
- 插件hook方式一般以hook  Instrumentation类为切入口
- 在execStartActivity方法中替换我们预先在宿主中配置的的activity,然后欺骗过检查之后在newActivity方法中替换成我们插件的activity的信息，这样就会启动我们目标插件的activity

1. 当我们执行startActivity的时候实际执行的是startActivityForResult

```java
 public void startActivityForResult(Intent intent, int requestCode) {
        if (mParent == null) {
            Instrumentation.ActivityResult ar =
                mInstrumentation.execStartActivity(
                    this, mMainThread.getApplicationThread(), mToken, this,
                    intent, requestCode);
            if (ar != null) {
                mMainThread.sendActivityResult(
                    mToken, mEmbeddedID, requestCode, ar.getResultCode(),
                    ar.getResultData());
            }
            if (requestCode >= 0) {

                mStartedActivity = true;
            }
        } else {
            mParent.startActivityFromChild(this, intent, requestCode);
        }
    }
```

可以看到这里执行了execStartActivity，我们看看此方法

```java
 public ActivityResult execStartActivity(
        Context who, IBinder contextThread, IBinder token, Activity target,
        Intent intent, int requestCode) {
        IApplicationThread whoThread = (IApplicationThread) contextThread;
        if (mActivityMonitors != null) {
            synchronized (mSync) {
                final int N = mActivityMonitors.size();
                for (int i=0; i<N; i++) {
                    final ActivityMonitor am = mActivityMonitors.get(i);
                    if (am.match(who, null, intent)) {
                        am.mHits++;
                        if (am.isBlocking()) {
                            return requestCode >= 0 ? am.getResult() : null;
                        }
                        break;
                    }
                }
            }
        }
        try {
            int result = ActivityManagerNative.getDefault()
                .startActivity(whoThread, intent,
                        intent.resolveTypeIfNeeded(who.getContentResolver()),
                        null, 0, token, target != null ? target.mEmbeddedID : null,
                        requestCode, false, false);
            checkStartActivityResult(result, intent);
        } catch (RemoteException e) {
        }
        return null;
    }

```

可以看到在这里进行了activity的校验checkStartActivityResult方法，所以需要在这个方法里面用宿主的activity欺骗他的检测



2.接下来的hook点就是我们需要替换成插件activity的地方，Instrumentation最终会执行ActivityThread的performLaunchActivity方法

```java
private final Activity performLaunchActivity(ActivityRecord r, Intent customIntent) {
        // System.out.println("##### [" + System.currentTimeMillis() + "] ActivityThread.performLaunchActivity(" + r + ")");

        ActivityInfo aInfo = r.activityInfo;
        if (r.packageInfo == null) {
            r.packageInfo = getPackageInfo(aInfo.applicationInfo,
                    Context.CONTEXT_INCLUDE_CODE);
        }

        ComponentName component = r.intent.getComponent();
        if (component == null) {
            component = r.intent.resolveActivity(
                mInitialApplication.getPackageManager());
            r.intent.setComponent(component);
        }

        if (r.activityInfo.targetActivity != null) {
            component = new ComponentName(r.activityInfo.packageName,
                    r.activityInfo.targetActivity);
        }

        Activity activity = null;
        try {
            java.lang.ClassLoader cl = r.packageInfo.getClassLoader();
            activity = mInstrumentation.newActivity(
                    cl, component.getClassName(), r.intent);
            r.intent.setExtrasClassLoader(cl);
            if (r.state != null) {
                r.state.setClassLoader(cl);
            }
        } catch (Exception e) {
            if (!mInstrumentation.onException(activity, e)) {
                throw new RuntimeException(
                    "Unable to instantiate activity " + component
                    + ": " + e.toString(), e);
            }
        }

        try {
            Application app = r.packageInfo.makeApplication(false, mInstrumentation);

            if (localLOGV) Slog.v(TAG, "Performing launch of " + r);
            if (localLOGV) Slog.v(
                    TAG, r + ": app=" + app
                    + ", appName=" + app.getPackageName()
                    + ", pkg=" + r.packageInfo.getPackageName()
                    + ", comp=" + r.intent.getComponent().toShortString()
                    + ", dir=" + r.packageInfo.getAppDir());

            if (activity != null) {
                ContextImpl appContext = new ContextImpl();
                appContext.init(r.packageInfo, r.token, this);
                appContext.setOuterContext(activity);
                CharSequence title = r.activityInfo.loadLabel(appContext.getPackageManager());
                Configuration config = new Configuration(mConfiguration);
                if (DEBUG_CONFIGURATION) Slog.v(TAG, "Launching activity "
                        + r.activityInfo.name + " with config " + config);
                activity.attach(appContext, this, getInstrumentation(), r.token,
                        r.ident, app, r.intent, r.activityInfo, title, r.parent,
                        r.embeddedID, r.lastNonConfigurationInstance,
                        r.lastNonConfigurationChildInstances, config);

                if (customIntent != null) {
                    activity.mIntent = customIntent;
                }
                r.lastNonConfigurationInstance = null;
                r.lastNonConfigurationChildInstances = null;
                activity.mStartedActivity = false;
                int theme = r.activityInfo.getThemeResource();
                if (theme != 0) {
                    activity.setTheme(theme);
                }

                activity.mCalled = false;
                mInstrumentation.callActivityOnCreate(activity, r.state);
                if (!activity.mCalled) {
                    throw new SuperNotCalledException(
                        "Activity " + r.intent.getComponent().toShortString() +
                        " did not call through to super.onCreate()");
                }
                r.activity = activity;
                r.stopped = true;
                if (!r.activity.mFinished) {
                    activity.performStart();
                    r.stopped = false;
                }
                if (!r.activity.mFinished) {
                    if (r.state != null) {
                        mInstrumentation.callActivityOnRestoreInstanceState(activity, r.state);
                    }
                }
                if (!r.activity.mFinished) {
                    activity.mCalled = false;
                    mInstrumentation.callActivityOnPostCreate(activity, r.state);
                    if (!activity.mCalled) {
                        throw new SuperNotCalledException(
                            "Activity " + r.intent.getComponent().toShortString() +
                            " did not call through to super.onPostCreate()");
                    }
                }
            }
            r.paused = true;

            mActivities.put(r.token, r);

        } catch (SuperNotCalledException e) {
            throw e;

        } catch (Exception e) {
            if (!mInstrumentation.onException(activity, e)) {
                throw new RuntimeException(
                    "Unable to start activity " + component
                    + ": " + e.toString(), e);
            }
        }

        return activity;
}
```
在这个方法里面进行activity的创建和生命周期的回调,这里调用了mInstrumentation.newActivity所以这里进行提换成我们插件的activity就可以，这里有个类加载器也必须使用插件的

3 .  要想实现上面的方法hook就必须替换Instrumentation而Instrumentation是ActivityThread的一个变量就可以hook进行替换


```java
public static boolean init() {
        //获取当前的ActivityThread对象
        Class<?> activityThreadClass = null;
        try {
            activityThreadClass = Class.forName(CLASS_ActivityThread);
            Method currentActivityThreadMethod = activityThreadClass.getDeclaredMethod(METHOD_currentActivityThread);
            currentActivityThreadMethod.setAccessible(true);
            Object currentActivityThread = currentActivityThreadMethod.invoke(null);


            //拿到在ActivityThread类里面的原始mInstrumentation对象
            Field instrumentationField = activityThreadClass.getDeclaredField(FIELD_mInstrumentation);
            instrumentationField.setAccessible(true);
            sActivityThreadInstrumentationField = instrumentationField;

            sInstrumentation = (Instrumentation) instrumentationField.get(currentActivityThread);
            sActivityThread = currentActivityThread;


            sActivityInstrumentationField =  Activity.class.getDeclaredField(FIELD_mInstrumentation);
            sActivityInstrumentationField.setAccessible(true);
            return true;
        } catch (ClassNotFoundException
                | NoSuchMethodException
                | IllegalAccessException
                | InvocationTargetException
                | NoSuchFieldException e) {
            e.printStackTrace();
        }

        return false;
    }
```

```java
public static void setInstrumentation(Object activityThread, HookedInstrumentation hookedInstrumentation) {
        try {
            sActivityThreadInstrumentationField.set(activityThread, hookedInstrumentation);
            if (Constants.DEBUG) Log.e(TAG, "set hooked instrumentation");
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
```
以上就实现了Instrumentation的替换，接下来就是创建我们自己的hookedInstrumentation类，这里是利用了静态代理，应为动态代理只能代理接口，而这里没有接口


4.hookedInstrumentation类

```java
public class HookedInstrumentation extends Instrumentation implements Handler.Callback {
    public static final String TAG = "HookedInstrumentation";
    protected Instrumentation mBase;
    private PluginManager mPluginManager;

    public HookedInstrumentation(Instrumentation base, PluginManager pluginManager) {
        mBase = base;
        mPluginManager = pluginManager;
    }

    /**
     * 覆盖掉原始Instrumentation类的对应方法,用于插件内部跳转Activity时适配
     *
     * @Override
     */
    public ActivityResult execStartActivity(
            Context who, IBinder contextThread, IBinder token, Activity target,
            Intent intent, int requestCode, Bundle options) {

        if (Constants.DEBUG) Log.e(TAG, "execStartActivity");
        //替换成当前宿主的activity信息
        mPluginManager.hookToStubActivity(intent);

        try {
            Method execStartActivity = Instrumentation.class.getDeclaredMethod(
                    "execStartActivity", Context.class, IBinder.class, IBinder.class,
                    Activity.class, Intent.class, int.class, Bundle.class);
            execStartActivity.setAccessible(true);
            return (ActivityResult) execStartActivity.invoke(mBase, who,
                    contextThread, token, target, intent, requestCode, options);
        } catch (Exception e) {
            e.printStackTrace();
            throw new RuntimeException("do not support!!!" + e.getMessage());
        }
    }

    @Override
    public Activity newActivity(ClassLoader cl, String className, Intent intent) throws InstantiationException, IllegalAccessException, ClassNotFoundException {
        if (Constants.DEBUG) Log.e(TAG, "newActivity");
         //替换成插件的activity信息
        if (mPluginManager.hookToPluginActivity(intent)) {
            String targetClassName = intent.getComponent().getClassName();
            PluginApp pluginApp = mPluginManager.getLoadedPluginApk();
            Activity activity = mBase.newActivity(pluginApp.mClassLoader, targetClassName, intent);
            activity.setIntent(intent);
            ReflectUtil.setField(ContextThemeWrapper.class, activity, Constants.FIELD_RESOURCES, pluginApp.mResources);
            return activity;
        }

        if (Constants.DEBUG) Log.e(TAG, "super.newActivity(...)");
        return super.newActivity(cl, className, intent);
    }

    @Override
    public boolean handleMessage(Message message) {
        if (Constants.DEBUG) Log.e(TAG, "handleMessage");
        return false;
    }


    @Override
    public void callActivityOnCreate(Activity activity, Bundle icicle) {
        if (Constants.DEBUG) Log.e(TAG, "callActivityOnCreate");
        super.callActivityOnCreate(activity, icicle);
    }
}
```
经过上面的流程下来就可以实现插件activity的跳转了和生命周期的回调

参考此代码：https://github.com/vimerzhao/PluginDemo




