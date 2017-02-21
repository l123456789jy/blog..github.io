---
layout:     post
title:      "viewgroup的事件分发"
subtitle:   ""
date:       2016-07-03 14:29:00
author:     "Lazy"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - Android
    - viewgroup
---







# viewgroup的事件分发
- 我们看下测试代码


```java
package com.example.administrator.myapplication.view;

import android.content.Context;
import android.util.AttributeSet;
import android.util.Log;
import android.view.MotionEvent;
import android.widget.RelativeLayout;

/**
 * Created by Administrator on 2015/11/29.
 */
public class MyRealLayout extends RelativeLayout {
    public MyRealLayout(Context context) {
        super(context);
    }
    public MyRealLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
    }
    public MyRealLayout(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        int action = ev.getAction();
        switch (action)
        {
            case MotionEvent.ACTION_DOWN:
                Log.e("MyRealLayout", "dispatchTouchEvent ACTION_DOWN");
                break;
            case MotionEvent.ACTION_MOVE:
                Log.e("MyRealLayout", "dispatchTouchEvent ACTION_MOVE");
                break;
            case MotionEvent.ACTION_UP:
                Log.e("MyRealLayout", "dispatchTouchEvent ACTION_UP");
                break;

            default:
                break;
        }
        boolean b = super.dispatchTouchEvent(ev);
        Log.e("MyRealLayout", "dispatchTouchEvent==="+b);
        return b;
    }
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        int action = ev.getAction();
        switch (action)
        {
            case MotionEvent.ACTION_DOWN:
                Log.e("MyRealLayout", "onInterceptTouchEvent ACTION_DOWN");
                break;
            case MotionEvent.ACTION_MOVE:
                Log.e("MyRealLayout", "onInterceptTouchEvent ACTION_MOVE");
                break;
            case MotionEvent.ACTION_UP:
                Log.e("MyRealLayout", "onInterceptTouchEvent ACTION_UP");
                break;
            default:
                break;
        }
        boolean b = super.onInterceptTouchEvent(ev);
        Log.e("MyRealLayout", "onInterceptTouchEvent==="+b);
        return b;
    }
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        int action = event.getAction();
        switch (action)
        {
            case MotionEvent.ACTION_DOWN:
                Log.e("MyRealLayout", "onTouchEvent ACTION_DOWN");
                break;
            case MotionEvent.ACTION_MOVE:
                Log.e("MyRealLayout", "onTouchEvent ACTION_MOVE");
                break;
            case MotionEvent.ACTION_UP:
                Log.e("MyRealLayout", "onTouchEvent ACTION_UP");
                break;
            default:
                break;
        }
        boolean b = super.onTouchEvent(event);
        Log.e("MyRealLayout", "onTouchEvent==="+b);
        return b;
    }
    @Override
    public void requestDisallowInterceptTouchEvent(boolean disallowIntercept)
    {
        Log.e("MyRealLayout", "requestDisallowInterceptTouchEvent ");
        super.requestDisallowInterceptTouchEvent(disallowIntercept);
    }
}


```
##button的点击事件的代码和上篇一样，我们看下log

            12-03 10:37:24.876 12965-12965/? E/MyRealLayout: dispatchTouchEvent ACTION_DOWN
            12-03 10:37:24.876 12965-12965/? E/MyRealLayout: onInterceptTouchEvent ACTION_DOWN
            12-03 10:37:24.876 12965-12965/? E/MyRealLayout: onInterceptTouchEvent===false
            12-03 10:37:24.876 12965-12965/? E/MyButton: dispatchTouchEvent ACTION_DOWN
            12-03 10:37:24.876 12965-12965/? E/mBtEvent: onTouch ACTION_DOWN
            12-03 10:37:24.876 12965-12965/? E/MyButton: onTouchEvent ACTION_DOWN
            12-03 10:37:24.877 12965-12965/? E/MyButton: onTouchEvent===true
            12-03 10:37:24.877 12965-12965/? E/MyButton: dispatchTouchEvent===true
            12-03 10:37:24.877 12965-12965/? E/MyRealLayout: dispatchTouchEvent===true
            12-03 10:37:24.923 12965-12965/? E/MyRealLayout: dispatchTouchEvent ACTION_UP
            12-03 10:37:24.923 12965-12965/? E/MyRealLayout: onInterceptTouchEvent ACTION_UP
            12-03 10:37:24.923 12965-12965/? E/MyRealLayout: onInterceptTouchEvent===false
            12-03 10:37:24.923 12965-12965/? E/MyButton: dispatchTouchEvent ACTION_UP
            12-03 10:37:24.923 12965-12965/? E/mBtEvent: onTouch ACTION_UP
            12-03 10:37:24.923 12965-12965/? E/MyButton: onTouchEvent ACTION_UP
            12-03 10:37:24.924 12965-12965/? E/MyButton: onTouchEvent===true
            12-03 10:37:24.924 12965-12965/? E/MyButton: dispatchTouchEvent===true
            12-03 10:37:24.924 12965-12965/? E/MyRealLayout: dispatchTouchEvent===true



##从上面的日志我们可以看出基本的流程是：
	MyRealLayout: dispatchTouchEvent ACTION_DOWN=====MyRealLayout: onInterceptTouchEvent ACTION_DOWN==MyButton: dispatchTouchEvent ACTION_DOWN====MyButton: onTouchEvent ACTION_DOWN======
    基本就是事件是从父VIEW分发到子viewd的



##然后我们进入源码查看：

- ViewGroup - dispatchTouchEvent - ACTION_DOWN



```java
 @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        if (!onFilterTouchEventForSecurity(ev)) {
            return false;
        }
		//获取当前的动作
        final int action = ev.getAction();
        final float xf = ev.getX();
        final float yf = ev.getY();
        //获取view的正确的坐标
        final float scrolledXFloat = xf + mScrollX;
        final float scrolledYFloat = yf + mScrollY;
        final Rect frame = mTempRect;
		//判断是否设置不拦截的标志
        boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
		//将mMotionTarget置为null
        if (action == MotionEvent.ACTION_DOWN) {
            if (mMotionTarget != null) {
                // this is weird, we got a pen down, but we thought it was
                // already down!
                // XXX: We should probably send an ACTION_UP to the current
                // target.``
                mMotionTarget = null;
            }	
            //disallowIntercept=true就表示不允许拦截，进入，disallowIntercept 可以通过viewGroup.requestDisallowInterceptTouchEvent(boolean);进行设置是否拦截而onInterceptTouchEvent(ev)我们可以复写自己控制返回值！
            // intercept
            if (disallowIntercept || !onInterceptTouchEvent(ev)) {
                // reset this event's action (just to protect ourselves)
                ev.setAction(MotionEvent.ACTION_DOWN);
                // We know we want to dispatch the event down, find a child
                // who can handle it, start with the front-most child.
                final int scrolledXInt = (int) scrolledXFloat;
                final int scrolledYInt = (int) scrolledYFloat;
                final View[] children = mChildren;
                final int count = mChildrenCount;
				//开始遍历子view注意这个遍历是倒着遍历的因为最后添加的view肯定在最上面！child.dispatchTouchEvent(ev)
                for (int i = count - 1; i >= 0; i--) {
                    final View child = children[i];
                    if ((child.mViewFlags & VISIBILITY_MASK) == VISIBLE
                            || child.getAnimation() != null) {
                        child.getHitRect(frame);
                        //进行判断当前点击的坐标是否包含子view如果包含这个子view就调用
                        if (frame.contains(scrolledXInt, scrolledYInt)) {
                            // offset the event to the view's coordinate system
                            final float xc = scrolledXFloat - child.mLeft;
                            final float yc = scrolledYFloat - child.mTop;
                            ev.setLocation(xc, yc);
                            child.mPrivateFlags &= ~CANCEL_NEXT_UP_EVENT;
                            //下面就是走view的事件分发流程了
                            if (child.dispatchTouchEvent(ev))  {
                                // Event handled, we have a target now.
                                mMotionTarget = child;
                                return true;
                            }
                            // The event didn't get handled, try the next view.
                            // Don't reset the event's location, it's not
                            // necessary here.
                        }
                    }
                }
            }
        }                                                                                                                          //down中做的事情就是判断是否需要拦截事件，如果不需要拦截就遍历字view执行view的分发

```



- ViewGroup - dispatchTouchEvent - ACTION_UP


```java
         public boolean dispatchTouchEvent(MotionEvent ev) {
                if (!onFilterTouchEventForSecurity(ev)) {
                    return false;
                }

                final int action = ev.getAction();
                final float xf = ev.getX();
                final float yf = ev.getY();
                final float scrolledXFloat = xf + mScrollX;
                final float scrolledYFloat = yf + mScrollY;
                final Rect frame = mTempRect;

                boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;

                if (action == MotionEvent.ACTION_DOWN) {...}

            boolean isUpOrCancel = (action == MotionEvent.ACTION_UP) ||
                        (action == MotionEvent.ACTION_CANCEL);
			//判读当前的事件是否等于up或者ACTION_CANCEL
            if (isUpOrCancel) {
                    mGroupFlags &= ~FLAG_DISALLOW_INTERCEPT;
                }
            final View target = mMotionTarget;
            if(target ==null ){...}
           //判断当前的view是否拦截
            if (!disallowIntercept && onInterceptTouchEvent(ev)) {...}

                if (isUpOrCancel) {
                    mMotionTarget = null;
                }

                // finally offset the event to the target's coordinate system and
                // dispatch the event.
                final float xc = scrolledXFloat - (float) target.mLeft;
                final float yc = scrolledYFloat - (float) target.mTop;
                ev.setLocation(xc, yc);

                return target.dispatchTouchEvent(ev);
            }
   up的时候主要是重置一些属性。然后在调用dispatchTouchEvent
```

##总结下
- dowun的时候ViewGroup捕获到事件去判断是否需要拦截事件，如果不需要拦截事件，就去遍历子view，倒着遍历然后循环的判断当前的点击区域是否包含当前的view，包含就会执行view的dispatchTouchEvent进行View的事件分发mMotionTarget就是childen的赋值
- up的时候ViewGroup捕获到事件去判断需不需要拦截事件，然后清空标记，调用view的dispatchTouchEvent进行View的事件分发
- movew的时候ViewGroup捕获到事件，然后判断是否拦截，如果没有拦截，则直接调用mMotionTarget.dispatchTouchEvent(ev)
- 分发之前的坐标都会经过修正之后传递给View当前的x，y分别减去child.left 和 child.top
- 就是为什么ViewGrou的onTouchEvent不会执行因为button设置了Ontouch，他一定会返回true，所以在dispatchTouchEvent就不会调用onTouchEvent



##关于拦截事件
- 如果你需要拦截，只要return true就行了，这要该事件就不会往子View传递了，并且如果你在DOWN retrun true ，则DOWN,MOVE,UP子View都不会捕获事件；如果你在MOVE return true , 则子View在MOVE和UP都不会捕获事件。
- 原因很简单，当onInterceptTouchEvent(ev) return true的时候，会把mMotionTarget 置为null ;
