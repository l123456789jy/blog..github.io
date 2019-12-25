---
layout:     post
title:      "搭建Android本地可信任的https服务器"
subtitle:   ""
date:       2016-07-24 14:07:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - Https
---







- 最近有个需求需要在手机本地搭建一个https服务器，然后让MediaPlayer来播放本地的视频。


- 最终搭建好之后，由于导入的证书是自制的证书，倒置MediaPlayer无法播放。

- 接下来去验证是不是这个原因造成。

1.我们知道，个人生成的证书都是不信任的，要想生成可信任的证书，我们就必须去一些机构去申请，这里可以推荐沃通，因为免费。

2.因为这里我们公司就已经有申请好的，所以直接拿来用了，我们只用jks那个文件，由于安卓不能识别jks，我们需要借助Portecle工具，去转换成kbsv-1格式一定要这个格式要不然报错

3.这里需要注意，生成的证书的域名一定要和你访问的域名一样，要不然还是不可信任的证书

4.证书生成是不能指定内网ip和本地域名的。

5.所以这里就坑了，我需要指定向本地怎么办，后来就想到了修改hosts，文件就可以了。

6.后来换成这个之后就可以啦！

##废话不都说上主要代码

```java
 /**
     * 开启本地服务
     */
    private void openSersice() throws IOException {

        try {
            AssetManager am = getAssets();
            InputStream ins2 = am.open("a.kbs");
            MyHttpd myHttpd = new MyHttpd();

            KeyStore keyStore = KeyStore.getInstance("BKS");
            keyStore.load(ins2, null);

            //读取证书,注意这里的密码必须设置为证书的密码
            KeyManagerFactory
                    keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
            keyManagerFactory.init(keyStore, "xxxxx".toCharArray());

            myHttpd.makeSecure(NanoHTTPD.makeSSLSocketFactory(keyStore, keyManagerFactory), null);
            myHttpd.start(NanoHTTPD.SOCKET_READ_TIMEOUT, false);


        } catch (IOException e) {
            Log.e("IOException", "Couldn't start server:\n" + e.getMessage());
        } catch (NumberFormatException e) {
            Log.e("NumberFormatException", e.getMessage());
        } catch (KeyStoreException | NoSuchAlgorithmException e) {
            Log.e("HTTPSException", "HTTPS certificate error:\n " + e.getMessage());
        } catch (UnrecoverableKeyException e) {
            Log.e("UnrecoverableKeyException", "UnrecoverableKeyException" + e.getMessage());
        }   catch (CertificateException e) {
            e.printStackTrace();
        }
    }

```

框架用的是NanoHTTPD
