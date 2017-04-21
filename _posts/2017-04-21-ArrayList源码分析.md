---
layout:     post
title:      "ArrayList源码分析"
subtitle:   ""
date:       2017-04-21 15:30:00
author:     ""
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - ArrayList
---

- 可以看到我们创建`ArrayLis`的时候指定了大小，会创建一个默认指定大小的数组



```java
 public ArrayList(int initialCapacity) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
    }

```

- 无参的会创建一个空数组

```java
 public ArrayList() {
        super();
        this.elementData = EMPTY_ELEMENTDATA;
    }

```


- 转换成数组并且copy到elementData数组中去

```java

  public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        size = elementData.length;
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    }

```


- 测量存放数据数组的大小

```java

 public void ensureCapacity(int minCapacity) {
     如果是空数组就是0，否则minExpand =10
        int minExpand = (elementData != EMPTY_ELEMENTDATA)
            // any size if real element table
            ? 0
            // larger than default for empty table. It's already supposed to be
            // at default size.
            : DEFAULT_CAPACITY;   默认大小是10

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }

```

- 进入到`ensureExplicitCapacity`



```java

 private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

```

```java
 private void grow(int minCapacity) {
        之前存放的数据大小
        int oldCapacity = elementData.length;、
        新增大小为=之前存放的数据大小+oldCapacity/2
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
        新增之后的数据大小还小于计算之后newCapacity大小，就以传递的大小为准
            newCapacity = minCapacity;
            判断当前增加的大小是否大于 Integer.MAX_VALUE - 8
        if (newCapacity - MAX_ARRAY_SIZE > 0)
        重新计算返回Integer.MAX_VALUE或者 Integer.MAX_VALUE - 8
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        拷贝扩大当前数据容量
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

```

```java
 private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }

```


```java
 contains判断可以里看出，是2个循环，至于这里为什么不用加强for因为Arratlisr实现了RandomAccess
public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

```


- get方法实现

```java
 public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }


 private void rangeCheck(int index) {
        if (index >= size)
        这个异常就是大家熟悉的角标越界
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }

    可以看到直接根据数组的角标返回因此查询比较快
     @SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }

```




- set方法实现



```java`

 public E set(int index, `E element) {
        rangeCheck(index);
        如果有数据就会返回当前位置的数据并且替换
        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }

```



- add方法

```java

  public boolean add(E e) {
        测量数组是否能存放不能就扩大
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        设置当前位置的数据并且集合size++
        elementData[size++] = e;
        return true;
    }


测量大小方法
 private void ensureCapacityInternal(int minCapacity) {
 如果之前没有添加数据，是空数组那就设置数组的大小默认为10
        if (elementData == EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }
         下面这个就是上面分析的扩大数组大小的方法
        ensureExplicitCapacity(minCapacity);
    }

```


` System.arraycopy`和``Arrays.copyOf`我们查看源码可以发现 `Arrays.copyOf` 底层还是` System.arraycopy`实现拷贝的


- remove方法

```java
 public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                清空的方法具体实现
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }



  private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
        重新拷贝个新的数组，从当前要删除的位置的下一位开始拷贝，此处可见删除只是重新copay的一份新的数组
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }
```


- clean方法

```java

   public void clear() {
        modCount++;

        // clear to let GC do its work  清空数组，等待GC回收
        for (int i = 0; i < size; i++)
            elementData[i] = null;

        size = 0;
    }

```



