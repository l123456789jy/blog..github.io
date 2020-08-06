---
layout:     post
title:      "BRVAH配合ConcatAdapter轻松实现多type布局"
subtitle:   ""
date:       2020-08-06 16:42:00
author:     ""
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:
tags:
    -
---


- 谷歌在  `androidx.recyclerview:recyclerview:1.2.0-alpha04` 这个版本引入了`ConcatAdapter`这个类，这个类和普通的Adapter有什么区别呢

- 简化了我们多条目类型的适配器的写法，高度解耦，每个条目你都可以定义一个Adapter

- 首先我们加入依赖
 `com.github.CymChad:BaseRecyclerViewAdapterHelper:3.0.4`
 `androidx.recyclerview:recyclerview:1.2.0-alpha04`

- 引入了`ConcatAdapter`和`BaseRecyclerViewAdapterHelper`，

- 建立我们的适配器

    class MyRecyclerViewAdapter1(layoutResId: Int, data: MutableList<String>?) :
        BaseQuickAdapter<String, BaseViewHolder>(layoutResId, data) {


        override fun convert(holder: BaseViewHolder, item: String) {
            holder.setText(R.id.tv__id_item_layout, "This is an Item, pos: $item");
        }
    }


- 使用`ConcatAdapter`

```java
  var arrayListOf = arrayListOf<String>()
        arrayListOf.add("1")
        arrayListOf.add("2")
        arrayListOf.add("3")
        arrayListOf.add("4")
        arrayListOf.add("我是第一个Adapter结束了")
        var adapter1 = MyRecyclerViewAdapter1(R.layout.id_rv_item_layout,arrayListOf)

        var arrayListOf2 = arrayListOf<String>()
        arrayListOf2.add("5")
        arrayListOf2.add("6")
        arrayListOf2.add("7")
        arrayListOf2.add("8")
        arrayListOf2.add("我是第二个Adapter结束了")
        var adapter2 = MyRecyclerViewAdapter1(R.layout.id_rv_item_layout,arrayListOf2)
        var car = ConcatAdapter(ConcatAdapter.Config.Builder().setIsolateViewTypes(true).build(),adapter1,adapter2)
        car.apply {
           addAdapter(adapter2)
        }
        appCompatImageView.adapter = car
```


- 这样我就看到了，将两个不同的适配器链接到一个适配器中，如果对于相同布局的需要复用可以使用`ConcatAdapter.Config.Builder().setIsolateViewTypes(true).build(),` 方法就可以了
