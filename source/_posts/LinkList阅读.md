---
thumbnail: /images/java.jpeg
title: LinkList阅读
date: 2019-11-04 13:25:19
tags:
	- java
	- 源码

---

{% asset_img 01.png %}




## LinkedList类字段

```java
// 双指针
transient Node<E> first;
transient Node<E> last;
```



## 存储Value的数据结构

```java
private static class Node<E> {
    E item;
  	// 双向链表
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

<!--more-->

##  add(E e):boolean 添加元素

```java
public boolean add(E e) {
    linkLast(e);
    return true;
}
```

```java
/**
 * Links e as last element.
 */
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
  	// 第一次链表为null
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```









## unlink(Node<E> x):Object删除某个节点

```java
/**
 * Unlinks non-null node x.
 */
E unlink(Node<E> x) {
    // assert x != null;
    final E element = x.item;
    final Node<E> next = x.next;
    final Node<E> prev = x.prev;
		
  	// 如果没有前项
    if (prev == null) {
      	// 删掉当前， 指向下一项
        first = next;
    } else {
      	// 前项的下一项等于 当前的下一项
        prev.next = next;
      	// 当前的前一项 设置为null
        x.prev = null;
    }

  	// 如果下一项为null
    if (next == null) {
      	
        last = prev;
    } else {
      	// 下一项的前一项 =  当前的前一项
        next.prev = prev;
      	// 设置x.next = null
        x.next = null;
    }
    x.item = null;
  
  	// 当前x状态{prev:null , item:null , next:null}
  
    size--;
    modCount++;
    return element;
}
```