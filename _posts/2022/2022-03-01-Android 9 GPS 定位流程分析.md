---
layout:     post
title:      "Android 9 GPS 定位流程分析"
subtitle:   ""
date:       2022-03-01 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags: Android
    -
---
- 涉及到的几个类

![image.png](https://upload-images.jianshu.io/upload_images/1205414-a2d82405b977d791.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- SystemServer 负责启动初始化 LocationManagerService添加到系统服务中
-  LocationManagerService 借助AIDL提供给应用层调用各个定位逻辑的管理者，管理着各个的privoder
-  LocationManager 暴露给app层调用AIDL接调用LocationManagerService
-  GnssLocationProvider 主要的GPS定位逻辑，调用HAL层GPS模块进行定位和一些gps.config配置文件初始化属性的加载

- 查看当前运行的模式可以执行：
  adb shell settings get secure location_providers_allowed  查看当前运行模式，模式可以为GPS 定位模式 ， network,gps 混合高精度定位 ，代码位置为 LocationSettings.java中，配置文件位置在frameworks/base/packages/SettingsProvider/res/values/defaults.xml def_location_providers_allowed 字段修改为 network,gps就是混合定位，只填写GPS就是GPS定位。

![时序图.jpg](https://upload-images.jianshu.io/upload_images/1205414-3f4395db14a86aba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 流程分析：
  - 在app中定位一般是
``` 
getSystemService(Context.LOCATION_SERVICE) as LocationManager，然后调用 requestLocationUpdates进行位置的监听
```
getSystemService 最终调用的是`ContextImpl`在到 `SystemServiceRegistry` 中创建 LocationManager，在LocationManager中创建了LocationManagerService的AIDL对象

``` 
 private void requestLocationUpdates(LocationRequest request, LocationListener listener,
            Looper looper, PendingIntent intent) {

        String packageName = mContext.getPackageName();

        // wrap the listener class
        ListenerTransport transport = wrapListener(listener, looper);

        try {
            mService.requestLocationUpdates(request, transport, intent, packageName);
       } catch (RemoteException e) {
           throw e.rethrowFromSystemServer();
       }
    }

```

这里可以看到最终调用的是 `mService` 的 requestLocationUpdates方法而 `mService` 就是我们的`LocationManagerService `  的AIDL对象

- `LocationManagerService ` 是什么时候初始化的？

      在`SystemServer` 初始化的时候初始化的我们可以在`startOtherServices`  方法中看到
``` 

            traceBeginAndSlog("StartLocationManagerService");
            try {
                location = new LocationManagerService(context);
                ServiceManager.addService(Context.LOCATION_SERVICE, location);
            } catch (Throwable e) {
                reportWtf("starting Location Manager", e);
            }
            traceEnd();

```
到此`LocationManagerService `创建完毕，接着我们看 LocationManagerService 中  requestLocationUpdates方法这里又调用到requestLocationUpdatesLocked方法

``` 

          private void requestLocationUpdatesLocked(LocationRequest request, Receiver receiver,
            int pid, int uid, String packageName) {
        // Figure out the provider. Either its explicitly request (legacy use cases), or
        // use the fused provider
        if (request == null) request = DEFAULT_LOCATION_REQUEST;
        这里获取具体的provder例如GPS ，net 这些，然后根据这个调用具体的provder对象
        String name = request.getProvider();
        if (name == null) {
            throw new IllegalArgumentException("provider name must not be null");
        }
        判断是否有创建对应的provider,所有的provider都实现LocationProviderInterface接口
        LocationProviderInterface provider = mProvidersByName.get(name);
        if (provider == null) {
            throw new IllegalArgumentException("provider doesn't exist: " + name);
        }

        UpdateRecord record = new UpdateRecord(name, request, receiver);
        if (D) {
            Log.d(TAG, "request " + Integer.toHexString(System.identityHashCode(receiver))
                    + " " + name + " " + request + " from " + packageName + "(" + uid + " "
                    + (record.mIsForegroundUid ? "foreground" : "background")
                    + (isThrottlingExemptLocked(receiver.mIdentity)
                    ? " [whitelisted]" : "") + ")");
        }

        UpdateRecord oldRecord = receiver.mUpdateRecords.put(name, record);
        if (oldRecord != null) {
            oldRecord.disposeLocked(false);
        }

        boolean isProviderEnabled = isAllowedByUserSettingsLocked(name, uid, mCurrentUserId);
        if (isProviderEnabled) {
            applyRequirementsLocked(name);
        } else {
            // Notify the listener that updates are currently disabled
            receiver.callProviderEnabledLocked(name, false);
        }
        // Update the monitoring here just in case multiple location requests were added to the
        // same receiver (this request may be high power and the initial might not have been).
        receiver.updateMonitoring(true);
    }


```

然后会进入到`applyRequirementsLocked` 方法


``` 

         private void applyRequirementsLocked(String provider) {
         #再次判断当前provder是否存在，如gps
        LocationProviderInterface p = mProvidersByName.get(provider);
        if (p == null) return;

        ArrayList<UpdateRecord> records = mRecordsByProvider.get(provider);
        WorkSource worksource = new WorkSource();
        ProviderRequest providerRequest = new ProviderRequest();

        ContentResolver resolver = mContext.getContentResolver();
        #默认后台的刷新时间是30分钟
        long backgroundThrottleInterval = Settings.Global.getLong(
                resolver,
                Settings.Global.LOCATION_BACKGROUND_THROTTLE_INTERVAL_MS,
                DEFAULT_BACKGROUND_THROTTLE_INTERVAL_MS);
        // initialize the low power mode to true and set to false if any of the records requires

        providerRequest.lowPowerMode = true;
        if (records != null) {
            for (UpdateRecord record : records) {
                if (isCurrentProfile(UserHandle.getUserId(record.mReceiver.mIdentity.mUid))) {
                    if (checkLocationAccess(
                            record.mReceiver.mIdentity.mPid,
                            record.mReceiver.mIdentity.mUid,
                            record.mReceiver.mIdentity.mPackageName,
                            record.mReceiver.mAllowedResolutionLevel)) {
                        LocationRequest locationRequest = record.mRealRequest;
                        long interval = locationRequest.getInterval();
                         #系统和白名单的应用那么不会受到后台刷新时间的限制
                        if (!isThrottlingExemptLocked(record.mReceiver.mIdentity)) {
                            if (!record.mIsForegroundUid) {
                                interval = Math.max(interval, backgroundThrottleInterval);
                            }
                            if (interval != locationRequest.getInterval()) {
                                locationRequest = new LocationRequest(locationRequest);
                                locationRequest.setInterval(interval);
                            }
                        }

                        record.mRequest = locationRequest;
                        providerRequest.locationRequests.add(locationRequest);
                        if (!locationRequest.isLowPowerMode()) {
                            providerRequest.lowPowerMode = false;
                        }
                        if (interval < providerRequest.interval) {
                            providerRequest.reportLocation = true;
                            providerRequest.interval = interval;
                        }
                    }
                }
            }

            if (providerRequest.reportLocation) {
                // calculate who to blame for power
                // This is somewhat arbitrary. We pick a threshold interval
                // that is slightly higher that the minimum interval, and
                // spread the blame across all applications with a request
                // under that threshold.
                long thresholdInterval = (providerRequest.interval + 1000) * 3 / 2;
                for (UpdateRecord record : records) {
                    if (isCurrentProfile(UserHandle.getUserId(record.mReceiver.mIdentity.mUid))) {
                        LocationRequest locationRequest = record.mRequest;

                        // Don't assign battery blame for update records whose
                        // client has no permission to receive location data.
                        if (!providerRequest.locationRequests.contains(locationRequest)) {
                            continue;
                        }

                        if (locationRequest.getInterval() <= thresholdInterval) {
                            if (record.mReceiver.mWorkSource != null
                                    && isValidWorkSource(record.mReceiver.mWorkSource)) {
                                worksource.add(record.mReceiver.mWorkSource);
                            } else {
                                // Assign blame to caller if there's no WorkSource associated with
                                // the request or if it's invalid.
                                worksource.add(
                                        record.mReceiver.mIdentity.mUid,
                                        record.mReceiver.mIdentity.mPackageName);
                            }
                        }
                    }
                }
            }
        }

        if (D) Log.d(TAG, "provider request: " + provider + " " + providerRequest);
       #开始调用具体的priover开启定位，这里如果是GPS就会走GnssLocationProvider的setRequest方法
        p.setRequest(providerRequest, worksource);
    }

```

这里的扩展点，所有的priover都是实现LocationProviderInterface接口，这里你需要实现自己的逻辑就可以添加自己的provider实现此接口，在在loadProvidersLocked方法注册你自己的provder就可以使用的

- 接下来进入到GPS的具体定位类 `GnssLocationProvider`,最终调用的是`updateRequirements`方法
``` 

         private void updateRequirements() {
        if (mProviderRequest == null || mWorkSource == null) {
            return;
        }

        boolean singleShot = false;

        // see if the request is for a single update
        if (mProviderRequest.locationRequests != null
                && mProviderRequest.locationRequests.size() > 0) {
            // if any request has zero or more than one updates
            // requested, then this is not single-shot mode
            singleShot = true;

            for (LocationRequest lr : mProviderRequest.locationRequests) {
                if (lr.getNumUpdates() != 1) {
                    singleShot = false;
                }
            }
        }

        if (DEBUG) Log.d(TAG, "setRequest " + mProviderRequest);
        if (mProviderRequest.reportLocation && !mDisableGps && isEnabled()) {
            // update client uids
            updateClientUids(mWorkSource);

            mFixInterval = (int) mProviderRequest.interval;
            mLowPowerMode = (boolean) mProviderRequest.lowPowerMode;
            // check for overflow
            if (mFixInterval != mProviderRequest.interval) {
                Log.w(TAG, "interval overflow: " + mProviderRequest.interval);
                mFixInterval = Integer.MAX_VALUE;
            }

            // 初始化一些GPS设置
            if (mStarted && hasCapability(GPS_CAPABILITY_SCHEDULING)) {
                // change period and/or lowPowerMode
                if (!native_set_position_mode(mPositionMode, GPS_POSITION_RECURRENCE_PERIODIC,
                        mFixInterval, 0, 0, mLowPowerMode)) {
                    Log.e(TAG, "set_position_mode failed in updateRequirements");
                }
            } else if (!mStarted) {
                /#开始调用HAL层开启定位请求
                startNavigating(singleShot);
            } else {
                // GNSS Engine is already ON, but no GPS_CAPABILITY_SCHEDULING
                mAlarmManager.cancel(mTimeoutIntent);
                if (mFixInterval >= NO_FIX_TIMEOUT) {
                    // set timer to give up if we do not receive a fix within NO_FIX_TIMEOUT
                    // and our fix interval is not short
                    mAlarmManager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP,
                            SystemClock.elapsedRealtime() + NO_FIX_TIMEOUT, mTimeoutIntent);                }
            }
        } else {
            updateClientUids(new WorkSource());

            stopNavigating();
            mAlarmManager.cancel(mWakeupIntent);
            mAlarmManager.cancel(mTimeoutIntent);
        }
    }

```
当这里执行到 startNavigating 方法中就开始调用 native_start 执行HAL层的GPS模块定位逻辑，以上就是正向请求逻辑


- 现在定位信息返回逻辑

当GPS模块更新位置时，GPS JNI层将调用GnssLocationProvider的handleReportLocation函数，其代码如下所示。

```
  private void handleReportLocation(boolean hasLatLong, Location location) {
        if (VERBOSE) Log.v(TAG, "reportLocation " + location.toString());
        #是否有速度
        if (location.hasSpeed()) {
            mItarSpeedLimitExceeded = location.getSpeed() > ITAR_SPEED_LIMIT_METERS_PER_SECOND;
        }

        if (mItarSpeedLimitExceeded) {
            Log.i(TAG, "Hal reported a speed in excess of ITAR limit." +
                    "  GPS/GNSS Navigation output blocked.");
            if (mStarted) {
                mGnssMetrics.logReceivedLocationStatus(false);
            }
            return;  // No output of location allowed
        }

        if (VERBOSE) Log.v(TAG, "reportLocation " + location.toString());

        // It would be nice to push the elapsed real-time timestamp
        // further down the stack, but this is still useful
        location.setElapsedRealtimeNanos(SystemClock.elapsedRealtimeNanos());
        location.setExtras(mLocationExtras.getBundle());

        try {
            #开始调用LocationManagerServise的reportLocation方法回调位置
            mILocationManager.reportLocation(location, false);
        } catch (RemoteException e) {
            Log.e(TAG, "RemoteException calling reportLocation");
        }

        if (mStarted) {
            mGnssMetrics.logReceivedLocationStatus(hasLatLong);
            if (hasLatLong) {
                if (location.hasAccuracy()) {
                    mGnssMetrics.logPositionAccuracyMeters(location.getAccuracy());
                }
                if (mTimeToFirstFix > 0) {
                    int timeBetweenFixes = (int) (SystemClock.elapsedRealtime() - mLastFixTime);
                    mGnssMetrics.logMissedReports(mFixInterval, timeBetweenFixes);
                }
            }
        }

        mLastFixTime = SystemClock.elapsedRealtime();
        // report time to first fix
        if (mTimeToFirstFix == 0 && hasLatLong) {
            mTimeToFirstFix = (int) (mLastFixTime - mFixRequestTime);
            if (DEBUG) Log.d(TAG, "TTFF: " + mTimeToFirstFix);
            if (mStarted) {
                mGnssMetrics.logTimeToFirstFixMilliSecs(mTimeToFirstFix);
            }

            // notify status listeners
            mListenerHelper.onFirstFix(mTimeToFirstFix);
        }

        if (mSingleShot) {
            stopNavigating();
        }

        if (mStarted && mStatus != LocationProvider.AVAILABLE) {
            // For devices that use framework scheduling, a timer may be set to ensure we don't
            // spend too much power searching for a location, when the requested update rate is slow.
            // As we just recievied a location, we'll cancel that timer.
            if (!hasCapability(GPS_CAPABILITY_SCHEDULING) && mFixInterval < NO_FIX_TIMEOUT) {
                mAlarmManager.cancel(mTimeoutIntent);
            }

            // send an intent to notify that the GPS is receiving fixes.
            Intent intent = new Intent(LocationManager.GPS_FIX_CHANGE_ACTION);
            intent.putExtra(LocationManager.EXTRA_GPS_ENABLED, true);
            mContext.sendBroadcastAsUser(intent, UserHandle.ALL);
            updateStatus(LocationProvider.AVAILABLE);
        }

        if (!hasCapability(GPS_CAPABILITY_SCHEDULING) && mStarted &&
                mFixInterval > GPS_POLLING_THRESHOLD_INTERVAL) {
            if (DEBUG) Log.d(TAG, "got fix, hibernating");
            hibernate();
        }
    }
```

通过上面又可以看到回调到 LocationManagerServise的reportLocation方法，最终会调用到handleLocationChangedLocked方法

```
 private void handleLocationChangedLocked(Location location, boolean passive) {
        if (D) Log.d(TAG, "incoming location: " + location);

        long now = SystemClock.elapsedRealtime();
        String provider = (passive ? LocationManager.PASSIVE_PROVIDER : location.getProvider());
        // Skip if the provider is unknown.
        LocationProviderInterface p = mProvidersByName.get(provider);
        if (p == null) return;
       #更新上一次最后的位置信息
        updateLastLocationLocked(location, provider);
        // mLastLocation should have been updated from the updateLastLocationLocked call above.
        Location lastLocation = mLastLocation.get(provider);
        if (lastLocation == null) {
            Log.e(TAG, "handleLocationChangedLocked() updateLastLocation failed");
            return;
        }

        // Update last known coarse interval location if enough time has passed.
        Location lastLocationCoarseInterval = mLastLocationCoarseInterval.get(provider);
        if (lastLocationCoarseInterval == null) {
            lastLocationCoarseInterval = new Location(location);
            mLastLocationCoarseInterval.put(provider, lastLocationCoarseInterval);
        }
        long timeDiffNanos = location.getElapsedRealtimeNanos()
                - lastLocationCoarseInterval.getElapsedRealtimeNanos();
        if (timeDiffNanos > LocationFudger.FASTEST_INTERVAL_MS * NANOS_PER_MILLI) {
            lastLocationCoarseInterval.set(location);
        }
        // Don't ever return a coarse location that is more recent than the allowed update
        // interval (i.e. don't allow an app to keep registering and unregistering for
        // location updates to overcome the minimum interval).
        Location noGPSLocation =
                lastLocationCoarseInterval.getExtraLocation(Location.EXTRA_NO_GPS_LOCATION);

        // Skip if there are no UpdateRecords for this provider.
        ArrayList<UpdateRecord> records = mRecordsByProvider.get(provider);
        if (records == null || records.size() == 0) return;

        // Fetch coarse location
        Location coarseLocation = null;
        if (noGPSLocation != null) {
            coarseLocation = mLocationFudger.getOrCreate(noGPSLocation);
        }

        // Fetch latest status update time
        long newStatusUpdateTime = p.getStatusUpdateTime();

        // Get latest status
        Bundle extras = new Bundle();
        int status = p.getStatus(extras);

        ArrayList<Receiver> deadReceivers = null;
        ArrayList<UpdateRecord> deadUpdateRecords = null;

        // Broadcast location or status to all listeners
        for (UpdateRecord r : records) {
            Receiver receiver = r.mReceiver;
            boolean receiverDead = false;

            int receiverUserId = UserHandle.getUserId(receiver.mIdentity.mUid);
            if (!isCurrentProfile(receiverUserId)
                    && !isUidALocationProvider(receiver.mIdentity.mUid)) {
                if (D) {
                    Log.d(TAG, "skipping loc update for background user " + receiverUserId +
                            " (current user: " + mCurrentUserId + ", app: " +
                            receiver.mIdentity.mPackageName + ")");
                }
                continue;
            }
            #黑名单app禁止使用定位 
            if (mBlacklist.isBlacklisted(receiver.mIdentity.mPackageName)) {
                if (D) {
                    Log.d(TAG, "skipping loc update for blacklisted app: " +
                            receiver.mIdentity.mPackageName);
                }
                continue;
            }

            if (!reportLocationAccessNoThrow(
                    receiver.mIdentity.mPid,
                    receiver.mIdentity.mUid,
                    receiver.mIdentity.mPackageName,
                    receiver.mAllowedResolutionLevel)) {

                if (D) {
                    Log.d(TAG, "skipping loc update for no op app: " +
                            receiver.mIdentity.mPackageName);
                }
                continue;
            }

            Location notifyLocation;
            if (receiver.mAllowedResolutionLevel < RESOLUTION_LEVEL_FINE) {
                notifyLocation = coarseLocation;  // use coarse location
            } else {
                notifyLocation = lastLocation;  // use fine location
            }
            if (notifyLocation != null) {
                Location lastLoc = r.mLastFixBroadcast;
                if ((lastLoc == null) || shouldBroadcastSafe(notifyLocation, lastLoc, r, now)) {
                    if (lastLoc == null) {
                        lastLoc = new Location(notifyLocation);
                        r.mLastFixBroadcast = lastLoc;
                    } else {
                        lastLoc.set(notifyLocation);
                    }

                    //DESAYSV AOSP IOV
                    if (D) { Log.i(TAG, "will call onLocationChanged on " + receiver); }
                    //DESAYSV AOSP IOV
                     #这里回掉了app层的位置信息回调，因为Listeners是封装在receiver里面的
                    if (!receiver.callLocationChangedLocked(notifyLocation)) {
                        Slog.w(TAG, "RemoteException calling onLocationChanged on " + receiver);
                        receiverDead = true;
                    }
                    r.mRealRequest.decrementNumUpdates();
                }
            }

            long prevStatusUpdateTime = r.mLastStatusBroadcast;
            if ((newStatusUpdateTime > prevStatusUpdateTime) &&
                    (prevStatusUpdateTime != 0 || status != LocationProvider.AVAILABLE)) {

                r.mLastStatusBroadcast = newStatusUpdateTime;
                if (!receiver.callStatusChangedLocked(provider, status, extras)) {
                    receiverDead = true;
                    Slog.w(TAG, "RemoteException calling onStatusChanged on " + receiver);
                }
            }

            // track expired records
            if (r.mRealRequest.getNumUpdates() <= 0 || r.mRealRequest.getExpireAt() < now) {
                if (deadUpdateRecords == null) {
                    deadUpdateRecords = new ArrayList<>();
                }
                deadUpdateRecords.add(r);
            }
            // track dead receivers
            if (receiverDead) {
                if (deadReceivers == null) {
                    deadReceivers = new ArrayList<>();
                }
                if (!deadReceivers.contains(receiver)) {
                    deadReceivers.add(receiver);
                }
            }
        }

        // remove dead records and receivers outside the loop
        if (deadReceivers != null) {
            for (Receiver receiver : deadReceivers) {
                removeUpdatesLocked(receiver);
            }
        }
        if (deadUpdateRecords != null) {
            for (UpdateRecord r : deadUpdateRecords) {
                r.disposeLocked(true);
            }
            applyRequirementsLocked(provider);
        }
    }

```

最终在 receiver.callLocationChangedLocked调用到
```
public boolean callStatusChangedLocked(String provider, int status, Bundle extras) {
            if (mListener != null) {
                try {
                    synchronized (this) {
                        #AIDL回调到App的位置监听
                        mListener.onStatusChanged(provider, status, extras);
                        // call this after broadcasting so we do not increment
                        // if we throw an exeption.
                        incrementPendingBroadcastsLocked();
                    }
                } catch (RemoteException e) {
                    return false;
                }
            } else {
                Intent statusChanged = new Intent();
                statusChanged.putExtras(new Bundle(extras));
                statusChanged.putExtra(LocationManager.KEY_STATUS_CHANGED, status);
                try {
                    synchronized (this) {
                        // synchronize to ensure incrementPendingBroadcastsLocked()
                        // is called before decrementPendingBroadcasts()
                        mPendingIntent.send(mContext, 0, statusChanged, this, mLocationHandler,
                                getResolutionPermission(mAllowedResolutionLevel),
                                PendingIntentUtils.createDontSendToRestrictedAppsBundle(null));
                        // call this after broadcasting so we do not increment
                        // if we throw an exeption.
                        incrementPendingBroadcastsLocked();
                    }
                } catch (PendingIntent.CanceledException e) {
                    return false;
                }
            }
            return true;
        }

```
这里最终通过AIDL的包装类ListenerTransport里面回调_handleMessage的mListener.onStatusChanged回调到App


- GPS JNI与HAL层流程

在GnssLocationProvider类中静态代码块中调用JNI在JNI中初始化了HAL层级 gnssHal_V1_1服务，在间接的调用HAL层

```
 static {
        class_init_native();
    }
```

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

最后在使用HAL层调用GPS模块

      