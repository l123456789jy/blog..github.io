---
layout:     post
title:      "webview与js交互"
subtitle:   ""
date:       2016-07-03 14:29:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - webview
---













##我们知道有时候我们需要和html交互怎么办呢？安卓大牛已经为我们提供了方法了，我们来看看吧~
- Android（Java）与JavaScript（HTML）交互有四种情况：
- 1） Android（Java）调用HTML中js代码
- 2） Android（Java）调用HTML中js代码（带参数）
- 3） HTML中js调用Android（Java）代码
- 4） HTML中js调用Android（Java）代码（带参数）




#废话不多说们直接上代码
```java
package com.example.administrator.myapplication;

import android.content.Context;
import android.os.Bundle;
import android.support.design.widget.FloatingActionButton;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.util.Log;
import android.view.View;
import android.webkit.JavascriptInterface;
import android.webkit.WebView;
import android.webkit.WebViewClient;
import android.widget.Button;
import android.widget.Toast;
import butterknife.Bind;
import butterknife.ButterKnife;

public class WebviewActivity extends AppCompatActivity {
    private static final String TAG = "WebviewActivity";
    private final String MESSAGE = "我是从安卓界面传递过来的";
    private  final String PATH="btn-paizhao.png";
    WebView wv;
    @Bind(R.id.toolbar) Toolbar mToolbar;
    @Bind(R.id.wv) WebView mWv;
    @Bind(R.id.bt) Button mBt;
    @Bind(R.id.fab) FloatingActionButton mFab;


    /**
     * 总结，如果只是单独传递字符串，可以不用把远端的html页面下载下来，直接调用，如果涉及到显示图片
     * 就需要下载下来了，你显示的是本地的照片他找不到照片的！
     * @param savedInstanceState
     */
    @Override protected void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_webview);
        ButterKnife.bind(this);
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
        setSupportActionBar(toolbar);
        initView();
    }


    private void initView() {
        wv = (WebView) findViewById(R.id.wv);
        //设置编码
        wv.getSettings().setDefaultTextEncodingName("utf-8");
        //支持js
        wv.getSettings().setJavaScriptEnabled(true);
          //wv.loadUrl("http://l123456789jy.github.io/blog/2015/01/16/test" +
              //  ".html");
        wv.loadUrl("file:///android_asset/main.html");
        //这样让js与webview建立起链接，webview里面的界面就可以使用 jsObj来调用安卓里面的写好的方法了
        wv.addJavascriptInterface(new JavaScriptObject(this), "jsObj");     //
        // jsObj 为桥连对象

        //监听加载完毕的事件！
        wv.setWebViewClient(new WebViewClient() {
            @Override
            public void onPageFinished(final WebView view, String url) {
                //  view.loadUrl("javascript:setData(" + detailshtml + ")");
                // 调用js里面写入好的方法
                view.loadUrl("javascript:showFromHtml()");
                Log.e(TAG, "onPageFinished");
            }
        });
        //调用网页的方法并且将数值传递过去
        mBt.setOnClickListener(new View.OnClickListener() {
            @Override public void onClick(View v) {
                wv.loadUrl("javascript: showFromHtml2('" + MESSAGE + "')");
                //设置图片
                wv.loadUrl("javascript: setImage('" + PATH + "')");
            }
        });
    }


    //这个是让js调用安卓的方法！
    public class JavaScriptObject {
        Context mContxt;


        JavaScriptObject(Context mContxt) {
            this.mContxt = mContxt;
        }


        //4.4以上必须加上
        @JavascriptInterface public void SetTitle(String titileName) {//获取标题！
            Toast.makeText(getApplicationContext(), titileName,
                    Toast.LENGTH_LONG).show();
        }


        @JavascriptInterface public void callNatvie() {
            Toast.makeText(getApplicationContext(), "收到网页的调用啦！",
                    Toast.LENGTH_LONG).show();
        }
    }
}


```

相信我在代码中已经写的很详细了，[我们看下htmL的代码](https://github.com/l123456789jy/AndroidTestDemo/blob/master/Demo/src/main/assets/main.html)



我相信通过上面的的两段代码，已经可以明白了，如果我们需要调用系统的照相机，这种情况我们只能，要么把html下载到本地，然后与调用，要么把图片上上传到服务器之后再将地址回掉给html来进行显示！

[整个完整的demO](https://github.com/l123456789jy/AndroidTestDemo/blob/master/Demo/src/main/java/com/example/administrator/myapplication/WebviewActivity.java)
