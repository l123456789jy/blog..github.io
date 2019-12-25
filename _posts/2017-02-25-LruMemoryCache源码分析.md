---
layout:     post
title:      "LruMemoryCache源码分析"
subtitle:   ""
date:       2017-02-25 15:32:00
author:     "Lazy"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
---




1. 目前在看UIL,看到了内存缓存，就纪记录下对LruMemoryCache得分析，看名字就知道他主要做得是实现内存缓存。

2.上注释代码

```
package com.nostra13.universalimageloader.cache.memory.impl;

import android.graphics.Bitmap;

import com.nostra13.universalimageloader.cache.memory.MemoryCache;

import java.util.Collection;
import java.util.HashSet;
import java.util.LinkedHashMap;
import java.util.Map;

/**
 * A cache that holds strong references to a limited number of Bitmaps. Each time a Bitmap is accessed, it is moved to
 * the head of a queue. When a Bitmap is added to a full cache, the Bitmap at the end of that queue is evicted and may
 * become eligible for garbage collection.<br />
 * <br />
 * <b>NOTE:</b> This cache uses only strong references for stored Bitmaps.
 *
 * @author Sergey Tarasevich (nostra13[at]gmail[dot]com)
 * @since 1.8.1
 */
public class LruMemoryCache implements MemoryCache {
    //创建一个LinkedHashMap
	private final LinkedHashMap<String, Bitmap> map;
    //缓存得最大内存大小
	private final int maxSize;
	/** Size of this cache in bytes */
	private int size;

	/** @param maxSize Maximum sum of the sizes of the Bitmaps in this cache */
	public LruMemoryCache(int maxSize) {
		if (maxSize <= 0) {
			throw new IllegalArgumentException("maxSize <= 0");
		}
		this.maxSize = maxSize;
		//LinkedHashMap他是一个双联表结构，通过在构造函数里面设置，accessOrder 为True,他就会记录访问顺序，将最新访问的元素添加到双向链表的表尾，并从原来的位置删除
		//这样就可以实现将最新访问得元素，存放倒底部。
		this.map = new LinkedHashMap<String, Bitmap>(0, 0.75f, true);
	}

	/**
	 * Returns the Bitmap for {@code key} if it exists in the cache. If a Bitmap was returned, it is moved to the head
	 * of the queue. This returns null if a Bitmap is not cached.
	 * 这个方法很简单就是取出Bitmap根据Uri
	 */
	@Override
	public final Bitmap get(String key) {
		if (key == null) {
			throw new NullPointerException("key == null");
		}

		synchronized (this) {
			return map.get(key);
		}
	}

	/** Caches {@code Bitmap} for {@code key}. The Bitmap is moved to the head of the queue. */
	@Override
	public final boolean put(String key, Bitmap value) {
		if (key == null || value == null) {
			throw new NullPointerException("key == null || value == null");
		}

		synchronized (this) {
			size += sizeOf(key, value);//每次存入bitmap得时候根据bitmap获取当前bitmap得宽和高相乘得到所占得内存大小以此累加，标记存入集合bitmap得容量
			Bitmap previous = map.put(key, value);//如果这个bitmap缓存中已经有了就会返回改Bitmap。
			if (previous != null) {
				size -= sizeOf(key, previous);//改Bitmap已经存进去过，那么就减去刚次标记得大小
			}
		}
        //计算删除最少使用得bitmap
		trimToSize(maxSize);
		return true;
	}

	/**
	 * Remove the eldest entries until the total of remaining entries is at or below the requested size.
	 *
	 * @param maxSize the maximum size of the cache before returning. May be -1 to evict even 0-sized elements.
	 */
	private void trimToSize(int maxSize) {
		while (true) {
			String key;
			Bitmap value;
			synchronized (this) {
				if (size < 0 || (map.isEmpty() && size != 0)) {
					throw new IllegalStateException(getClass().getName() + ".sizeOf() is reporting inconsistent results!");
				}
                //如果当前得大小不超过指定得大小直接退出循环
				if (size <= maxSize || map.isEmpty()) {
					break;
				}
                //获取map得栈顶最不常使用得bitmap
				Map.Entry<String, Bitmap> toEvict = map.entrySet().iterator().next();
				if (toEvict == null) {
					break;
				}
				key = toEvict.getKey();
				value = toEvict.getValue();
				map.remove(key);//删除该bitmap
				size -= sizeOf(key, value);//大小标记减少
			}
		}
	}

	/** Removes the entry for {@code key} if it exists. */
	@Override
	public final Bitmap remove(String key) {
		if (key == null) {
			throw new NullPointerException("key == null");
		}

		synchronized (this) {
			Bitmap previous = map.remove(key);//删除指定得bitmap
			if (previous != null) {
				size -= sizeOf(key, previous);
			}
			return previous;
		}
	}

	@Override
	public Collection<String> keys() {
		synchronized (this) {
			return new HashSet<String>(map.keySet());
		}
	}

	@Override
	public void clear() {//清空所有得缓存
		trimToSize(-1); // -1 will evict 0-sized elements
	}

	/**
	 * Returns the size {@code Bitmap} in bytes.
	 * <p/>
	 * An entry's size must not change while it is in the cache.
	 */
	private int sizeOf(String key, Bitmap value) {
		return value.getRowBytes() * value.getHeight();
	}

	@Override
	public synchronized final String toString() {
		return String.format("LruCache[maxSize=%d]", maxSize);
	}
}

```

已经就是对源码得注释，此处好处是都实现了MemoryCache 接口，所以上层调用只要你实现MemoryCache 接口，如果有一天你觉得他这个缓存实现得不爽，你自己也可以实现自己得缓存实现，只要实现MemoryCache 接口就可以了。