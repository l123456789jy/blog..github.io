---
layout:     post
title:      "使用ViewModel在Fragment之间共享数据"
subtitle:   ""
date:       2020-07-19 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    - ViewModel
---



- 我们之前在Activity和Fagment之间传值一般使用构造，回调。等等方式，但是这种存在内存泄漏和空构造异常，现在官方提供了更友好的ViewModel进行传值，并且可以
跟随Activity和Fragment的生命周期，完全解耦

- 这个是官方地址：https://developer.android.google.cn/topic/libraries/architecture/viewmodel


- 需要依赖Fragment的扩展函数库   ` implementation "androidx.fragment:fragment-ktx:1.2.5"`

- 我们建立一个需要传值的Viemodel类，这里只是简单传递个boolean值


```java
package com.example.jitpackdemo.ui.main

import android.content.ClipData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel

class SharedViewModel : ViewModel() {
    val selected = MutableLiveData<Boolean>()
}
```



- 在Mactivity中关联此ViewModel


```java
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.main_activity)
        var shareModel = ViewModelProviders.of(this).get(SharedViewModel::class.java)
        if (savedInstanceState == null) {
            supportFragmentManager.beginTransaction()
                .replace(R.id.container, MainFragment.newInstance())
                .commitNow()
        }
        shareModel.selected.value= true
    }
}
```

可以看到这里我们区改变了数值为true

- Fragment中我们只需要`    private val model: SharedViewModel by activityViewModels1()` 引入刚才我们创建的viewModel

```java
class MainFragment : Fragment() {

    companion object {
        fun newInstance() = MainFragment()
    }

    private lateinit var viewModel: MainViewModel
    private val model: SharedViewModel by activityViewModels1()
    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                              savedInstanceState: Bundle?): View {
        return inflater.inflate(R.layout.main_fragment, container, false)
    }

    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)
        viewModel = ViewModelProviders.of(this).get(MainViewModel::class.java)
        model.selected.observe(viewLifecycleOwner, Observer {
            Toast.makeText(activity,it.toString(),Toast.LENGTH_SHORT).show()
        })
    }

}
```

这样我们就可以在Fragment中正常收到我们从Activity的数值了

