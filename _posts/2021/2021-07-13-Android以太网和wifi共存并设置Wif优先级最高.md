---
layout:     post
title:      "Android以太网和wifi共存并设置Wif优先级最高"
subtitle:   ""
date:       2021-07-13 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags: Android
    -
---


- 在Android5.0以后网络请求的优先级，不在以devcie下的config文件配置的顺序为主，而根据网络的连接状态，等其他因素综合动态评分机制，进行判断优先级。
- 默认的优先级为

1. Wifi初始分值为60（WifiStateMachine.java）；
2. Ethernet初始分值为70（EthernetNetworkFactory.java）；
3. Mobile network初始分值为50（DataConnection.java）
4. bt-pan初始分值为69（BluetoothTetheringNetworkFactory.java）：
   在实际运行中，还会根据网络的实时状态调整分值,所以这里可以看到Android系统默认是以太网的优先级最高，wif最高优先级为60大于60也是60，以太网根据网卡的up和down状态，把分值设置为70（NETWORK_SCORE）或0。

- 所以如果需要达到，默认设置wif为优先级最高，需要修改 `ConnectivityService`的 ` nai.asyncChannel.disconnect()` 注释，达到两个网络可以共存，

```
private void teardownUnneededNetwork(NetworkAgentInfo nai) {
if (nai.numRequestNetworkRequests() != 0) {
for (int i = 0; i < nai.numNetworkRequests(); i++) {
NetworkRequest nr = nai.requestAt(i);
// Ignore listening requests.
if (nr.isListen()) continue;
loge("Dead network still had at least " + nr);
break;
}
}
// nai.asyncChannel.disconnect();
}
```
1.ip route show //显示路由
2.ip rule list //显示路由表规则优先级
3.ip route flush cache   //刷新路由缓存
4.ifconfig usb0 down 关闭usb0网络接口
5.ifconfig usb0 up 打开usb0网络接口
6. svc wifi enable 打开wif
7.svc wifi disable 关闭wif
8  ip route get 47.110.189.28 查看某个IP访问的路由
9. cat /proc/net/route
10. ip route list table main



此时只是wif和以太网能共存，但是这个时候由于以太网的优先级比，wif优先级高，因此我们需要改动`EthernetNetworkFactory` 的NETWORK_SCORE小于60即可，还需要将WifiStateMachine的优先级设置为Integer.MAX_VALUE，这样确保wif的优先级为最高
，此时还会有个问题，就是wif连接以后会自动断开以太网，我们需要做下面处理，不要释放连接

```
  @Override
    protected void releaseNetworkFor(NetworkRequest networkRequest) {
        NetworkInterfaceState network = networkForRequest(networkRequest);
        if (network == null) {
            Log.e(TAG, "needNetworkFor, failed to get a network for " + networkRequest);
            return;
        }

        if (--network.refCount == 1) {

//            network.stop();
        }
    }
```

以上变可以实现以太网和Wif共存共同上网，基于Android9版本

- 参考文章  https://blog.csdn.net/u013270171/article/details/103249846
- https://blog.csdn.net/u010961631/article/details/48971651
- https://blog.csdn.net/qq_32072451/article/details/73826030









