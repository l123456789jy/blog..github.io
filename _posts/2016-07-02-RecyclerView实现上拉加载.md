---
layout:     post
title:      "RecyclerView实现上拉加载"
subtitle:   ""
date:       2016-07-02 14:29:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - RecyclerView
---







## RecyclerView是 Android 兼容包V21中新推出的列表类，它的自定义化强的优点足以让它能够取代GridView和ListView，本文将结合SwipeRefreshLayout与RecyclerView讲解如何实现下拉刷新和自动加载的代码,不废话直接上代码！
### 需要的依赖：
`compile 'com.android.support:appcompat-v7:21.0.0'
compile 'com.android.support:recyclerview-v7:21.0.0'
compile 'com.android.support:cardview-v7:21.0.0'
compile 'com.android.support:support-v4:21.0.0'`
### 我们下拉刷新直接使用

```java
android.support.v4.widget.SwipeRefreshLayout
这个库来使用，符合MD风格的下拉刷新！
```
## 看看我们的xml

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                tools:context=".MainActivity">

           <android.support.v4.widget.SwipeRefreshLayout
                android:id="@+id/swipe_container"
                xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent">
            <android.support.v7.widget.RecyclerView
                android:id="@+id/my_recycler_view"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                />
</android.support.v4.widget.SwipeRefreshLayout>
</RelativeLayout>
```
##下面放出我们的主要代码
```java
package suzhou.dataup.cn.myapplication.fragment;

import android.app.Fragment;
import android.support.v4.widget.SwipeRefreshLayout;
import android.support.v7.widget.DefaultItemAnimator;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.widget.Toast;

import com.squareup.okhttp.Request;
import com.squareup.okhttp.Response;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import butterknife.ButterKnife;
import butterknife.InjectView;
import suzhou.dataup.cn.myapplication.R;
import suzhou.dataup.cn.myapplication.adputer.AndroidResoutAdputer;
import suzhou.dataup.cn.myapplication.base.BaseFragment;
import suzhou.dataup.cn.myapplication.bean.HomeResoutBean;
import suzhou.dataup.cn.myapplication.callback.MyHttpCallBcak;
import suzhou.dataup.cn.myapplication.constance.CountUri;
import suzhou.dataup.cn.myapplication.mangers.OkHttpClientManager;
import suzhou.dataup.cn.myapplication.utiles.LogUtil;

/**
 * A simple {@link Fragment} subclass.
 * Activities that contain this fragment must implement the
 * {@link AndroidFragment.OnFragmentInteractionListener} interface
 * to handle interaction events.
 * Use the {@link AndroidFragment#newInstance} factory method to
 * create an instance of this fragment.
 * 安卓控件的界面的界面
 */
public class AndroidFragment extends BaseFragment {
    int lastVisibleItem = 0;
    int index = 1;
    int temp = 0;
    @InjectView(R.id.my_recycler_view)
    RecyclerView recyclerView;
    @InjectView(R.id.swipe_container)
    SwipeRefreshLayout mSwipeContainer;
    AndroidResoutAdputer mMyadputer;
    List<HomeResoutBean.ResultsEntity> mResultsEntityList = new ArrayList<>();
    boolean isFirstLoda = true;

    public AndroidFragment() {
        super(R.layout.fragment_weal);
    }

    @Override
    protected void initHead() {
    }

    @Override
    protected void initContent() {
        // 创建一个线性布局管理器
        final LinearLayoutManager mLayoutManager = new LinearLayoutManager(getActivity());
        recyclerView.setLayoutManager(mLayoutManager);//设置线性的管理器！
        //设置刷新时的不同的颜色！
        mSwipeContainer.setColorScheme(android.R.color.holo_blue_bright, android.R.color.holo_green_light, android.R.color.holo_orange_light, android.R.color.holo_red_light);
        //google官方的下拉刷新！
        mSwipeContainer.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
            @Override
            public void onRefresh() {
                lastVisibleItem = 0;
                isFirstLoda = true;
                mResultsEntityList.clear();
                index = 1;
                getData(index);

            }
        });
        //监听recyclerView的上滑动的位置来进行积蓄的加载更多的数据
        recyclerView.setOnScrollListener(new RecyclerView.OnScrollListener() {
            //滚动中调用
            @Override
            public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                //获取总的适配器的数量
                int totalCount = mMyadputer.getItemCount();
                LogUtil.e("总的数目  " + totalCount);
                LogUtil.e("滚动的状态  " + newState);
                //这个就是判断当前滑动停止了，并且获取当前屏幕最后一个可见的条目是第几个，当前屏幕数据已经显示完毕的时候就去加载数据
                if (newState == RecyclerView.SCROLL_STATE_IDLE && lastVisibleItem + 1 == mMyadputer.getItemCount()) {
                    mSwipeContainer.setRefreshing(true);//刷新完毕!
                    //请求数据
                    index++;
                    getData(index);
                }
            }

            //滚动停止后调用
            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                //获取最后一个可见的条目的位置,如果是线性加载更多就换成这个
                lastVisibleItem = mLayoutManager.findLastVisibleItemPosition();
            }
        });
    }

    @Override
    protected void initLocation() {
        getData(index);
    }


    @Override
    protected void initLogic() {
    }

    @Override
    protected void isShow() {
        if (mMyadputer != null) {
            lastVisibleItem = 0;
            isFirstLoda = true;
            mResultsEntityList.clear();
            index = 1;
            getData(index);
            LogUtil.e("可见了");
        }

    }

    @Override
    protected void isGone() {

    }

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        ButterKnife.reset(this);
    }

    //获取福利的数据
    private void getData(int index) {
        OkHttpClientManager.get(CountUri.BASE_URI + "/Android/20/" + index + "", new MyHttpCallBcak() {
            @Override
            public void onFailure(Request request, IOException e) {
                getActivity().runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        mSwipeContainer.setRefreshing(false);//刷新完毕!
                    }
                });
            }

            @Override
            public void onResponse(final Response response) {
                try {
                    if (response != null) {
                        HomeResoutBean homeResoutBean = mGson.fromJson(response.body().string(), HomeResoutBean.class);
                        List<HomeResoutBean.ResultsEntity> results = homeResoutBean.results;
                        for (HomeResoutBean.ResultsEntity result : results) {
                            mResultsEntityList.add(result);
                        }
                        if (isFirstLoda) {
                            getActivity().runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    mSwipeContainer.setRefreshing(false);//刷新完毕!
                                    mMyadputer = new AndroidResoutAdputer(mResultsEntityList, options_base, mLayoutUtil);
                                    recyclerView.setAdapter(mMyadputer);
                                    recyclerView.setItemAnimator(new DefaultItemAnimator());
                                    isFirstLoda = false;
                                }
                            });
                        } else {
                            getActivity().runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    mSwipeContainer.setRefreshing(false);//刷新完毕!
                                    mMyadputer.notifyDataSetChanged();
                                }
                            });

                        }
                    } else {
                        getActivity().runOnUiThread(new Runnable() {
                            @Override
                            public void run() {
                                Toast.makeText(getActivity(), "获取服务器数据失败。。。", Toast.LENGTH_SHORT).show();
                            }
                        });

                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
    }
}

```
##最重要的说明已经在代码注释中有了，主要就是监听rcy的滚动监听，然后监听滑动停止时候的位置


##如果是瀑布流如何实现上拉加载更多呢！so easy请看代码

```java
   //滚动停止后调用
            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                //获取最后一个可见的条目的位置,如果是线性加载更多就换成这个我们只需要监听他的最后一个返回的位置就行啦！比较最大哪个数值就是最后一个显示的postion
//                lastVisibleItem = mLayoutManager.findLastVisibleItemPosition();
                int[] firstVisibleItemPositions = mLayoutManager.findLastVisibleItemPositions(null);
                for (int firstVisibleItemPosition : firstVisibleItemPositions) {
                    temp = firstVisibleItemPosition;
                    if (lastVisibleItem < temp) {
                        lastVisibleItem = firstVisibleItemPosition;//标记最后一个显示的postion
                        LogUtil.e("停止可见的位置是  " + firstVisibleItemPosition);
                    }
                }
            }
        });
```
##TIP 我们如何设置自动刷新呢！其实谷歌已经提供方法了但是很遗憾这个是私有的方法，怎么办呢！反射调用呗！上菜。。

```java
  package suzhou.dataup.cn.myapplication.utiles;

import android.support.v4.widget.SwipeRefreshLayout;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

/**
 * 作者：liujingyuan on 2015/11/3 21:51
 * 邮箱：906514731@qq.com
 */
public class SwipContainerUtiles {
    //通过反射调用起私有的自动刷新的方法！
    public static void setRefreshing(SwipeRefreshLayout refreshLayout, boolean refreshing, boolean notify) {
        Class<? extends SwipeRefreshLayout> refreshLayoutClass = refreshLayout.getClass();
        if (refreshLayoutClass != null) {

            try {
                Method setRefreshing = refreshLayoutClass.getDeclaredMethod("setRefreshing", boolean.class, boolean.class);
                setRefreshing.setAccessible(true);
                setRefreshing.invoke(refreshLayout, refreshing, notify);
            } catch (NoSuchMethodException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            }
        }
    }

}

```
##好了以上就可以轻松的实现自动刷新和上拉加载啦！Demo地址在 [https://github.com/l123456789jy/FilveFlavours](https://github.com/l123456789jy/FilveFlavours)
