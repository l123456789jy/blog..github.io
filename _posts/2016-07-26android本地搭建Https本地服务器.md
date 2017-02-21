---
layout:     post
title:      "android本地搭建Https本地服务器"
subtitle:   "有时候我们需要在手机内部开启一个代理，进行一些转发"
date:       2016-11-24 11:30:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - 本地服务
---








##在本地搭建服务器有什么用呢？
     这个我只能说我现在用着了，需要加密HLS视频，具体的应用场景可以很多。



##搭建普通的一个http服务器好搞，https怎么做呢
- 首先我们这次使用的是一个比较好的三方的框架NanoHTTPD
- 但是悲剧的是他并没有实现android本地https具体的例子。



##首先我们知道https需要证书，怎么生成呢，

- 使用keytool生成证书keytool是JDK提供的管理加密密钥、X.509证书链和可信证书密钥库的简便工具。安卓开发必定安装了JDK并且一般都会配置好环境变量，所以你可以直接在终端或DOC窗口输入keytool命令来查看帮助。

- 生成密钥对

        keytool -genkey -alias server -keyalg RSA -keystore server.jks
       -alias后面跟的是唯一别名,-keystore后面填保存秘钥对的文件路径还可以添加一个-validity 天数声明有效期


需要注意的地方：执行命令之后第一个问题让你输入名字的地方最好设置成域名，比如这样baidu.com或者这样localhost，反正匹配你要调式的域名就对了,当然，如果你在安卓上调试，那么本地地址可能用不了。



- 导出证书

 上面生成了服务端使用的密钥对，现在可以通过它生成证书给客户端使用


    keytool -export -alias server -storepass 123456 -keystore server.jks -file server.cer



这个证书是服务端使用的我们客户端使用的kbs


- 生成客户端密钥


      keytool -genkey -alias android -keyalg RSA -keystore android.jks


- 刚才生成了客户端的密钥库android.jks。但是安卓默认是不支持jks格式的！比较常规的解决方式是用Portecle工具将它转换成bks文件。

- 下载完之后解压并在目录下运行命令：java -jar portecle.jar或者也可以直接双击它打开

运行之后就会出来UI界面，用它打开android.jks然后选菜单 Tools –> Change Keystore Type –> BKS 在弹出框输入密码进行转换，最后别忘记选菜单 File –> Save Keystore As 将它另存为android.kbs(名字随意)


- 生成kbs文件之后，把它放到安卓的目录下assets或者raw。（注意这里有个坑，这个kbs文件我们是不能用的，我们需要装换成kbsv-1）v-1,还是用上面的工具转换


生成之后怎么做呢


```java
 try {
            AssetManager am = getAssets();
            //InputStream ins1 = am.open("server.cer");
            InputStream ins2 = am.open("android.kbs");
            MyHttpd myHttpd = new MyHttpd();

            KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
            keyStore.load(ins2, null);

            //读取证书,注意这里的密码必须设置
            KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
            keyManagerFactory.init(keyStore, "android".toCharArray());

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

```
这样我们就可以在本地使用https访问了，注意这证书是自己生成的所以需要同意加载才行，默认认为是不安全的
