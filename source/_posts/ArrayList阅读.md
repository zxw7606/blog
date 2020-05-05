---
thumbnail: /images/java.jpeg
title: ArrayList阅读
date: 2019-11-04 08:37:34
tags:
	- java
	- 源码

---



{% asset_img al.png  %}



### grow(int minCapacity) :void增长数组

```java
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     * if (minCapacity - elementData.length > 0) =>
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
      	// 旧数组的大小
        int oldCapacity = elementData.length;
      	// 新数组的大小 = 旧数组的大小 + 旧数组的大小/2；
        int newCapacity = oldCapacity + (oldCapacity >> 1);
      
      	// 处理 minCapacity的要求
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
      	// if newCapacity > Integer.MAX_VALUE - 8;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```





<!--more-->

###  indexOf(Object o) :int 增长数组

```java
public boolean contains(Object o) {
  return indexOf(o) >= 0;
}

public int indexOf(Object o) {
    if (o == null) {
      	// 返回为null的
        for (int i = 0; i < size; i++)
            if (elementData[i]==null)
                return i;
    } else {
      	// 返回第一个相等的 
        for (int i = 0; i < size; i++)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}
```



### remove(int index):Object 移除

```java
public E remove(int index) {
    rangeCheck(index);

    modCount++;
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}
```



例如:

Arraylist = {0,1,2,3}

 remove(2)

numMoved = 1;  要移动的数量  即index之后的所有数

将index + 1的 位置 全部往前挪

System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);



```java
public static native void arraycopy(Object src,  int  srcPos,
                                    Object dest, int destPos,
                                    int length);


```


方法示例:
```java
int a[] = {1,2,3};
int b[] = {4,5,6,0,0,0};
System.arraycopy(src = a, srcPos=0, dest=b, destPos=3, lenght=3);
// b = {4,5,6,1,2,3}
```











