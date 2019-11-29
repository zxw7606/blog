---
thumbnail: /images/java.jpeg
title: HashMap阅读
date: 2019-11-04 14:25:06
tags:
	- java
	- 源码
---





## Value结构

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
  	// 拉链的下一个节点
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```





<!--more-->

```java
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
  	// 如果 table 不为null 并且 table 长度大于0 并且
  	// （n-1) & hash
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
      	// 如果第一个节点的hash == hash 并且 key 也相等的话
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
      	// 如果第一个节点不相等 那么
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
              	// 从红黑树里面找
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
              // 从普通的链表找
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```





## TreeNode结构

```java
/**
 * Entry for Tree bins. Extends LinkedHashMap.Entry (which in turn
 * extends Node) so can be used as extension of either regular or
 * linked node.
 */
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
  TreeNode<K,V> parent;  // red-black tree links
  TreeNode<K,V> left;
  TreeNode<K,V> right;
  TreeNode<K,V> prev;    // needed to unlink next upon deletion
  boolean red;
  TreeNode(int hash, K key, V val, Node<K,V> next) {
    super(hash, key, val, next);
  }

  /**
   * Returns root of tree containing this node.
   */
  final TreeNode<K,V> root() {
    for (TreeNode<K,V> r = this, p;;) {
      // 如果是根节点的话  那么返回
      if ((p = r.parent) == null)
        return r;
      r = p;
    }
  }
  
  
   /**
    * Finds the node starting at root p with the given hash and key.
    * The kc argument caches comparableClassFor(key) upon first use
    * comparing keys.
    */
    final TreeNode<K,V> find(int h, Object k, Class<?> kc) {
      // 根节点
      TreeNode<K,V> p = this;
      do {
        int ph, dir; K pk;
        
        TreeNode<K,V> pl = p.left, pr = p.right, q;
        // 如果当前的hash 比 hash大的话
        if ((ph = p.hash) > h)
          // 左边找
          p = pl; 
        else if (ph < h)
          // 右边找
          p = pr;
        // 如果 node.key 和 k 相等的话
        else if ((pk = p.key) == k || (k != null && k.equals(pk)))
          return p;
        /* ==============（ph == h） ============ */
        
        // pl == null 
        else if (pl == null)
          p = pr;
        // pr == null
        else if (pr == null)
          p = pl;
        // keyClass !=null 
        else if ((kc != null ||
                  // key 实现了 comparable<k.class>
                  (kc = comparableClassFor(k)) != null) &&
                 // 用 comparable 比较 k，px
                 (dir = compareComparables(kc, k, pk)) != 0)
          // 小了左边 大了右边
          p = (dir < 0) ? pl : pr;
        // keyClass == null 继续找普通的那种
        else if ((q = pr.find(h, k, kc)) != null)
          return q;
        else
          p = pl;
      } while (p != null);
      return null;
    }
}
```



##  comparableClassFor(Object x) &&  compareComparables(Class<?> kc, Object k, Object x)

```java
/**
 * Returns x's Class if it is of the form "class C implements
 * Comparable<C>", else null.
 * 返回x 的类如果 x是 实现了Comparable
 * 没有则为null?
 */
static Class<?> comparableClassFor(Object x) {
    if (x instanceof Comparable) {
        Class<?> c; Type[] ts, as; Type t; ParameterizedType p;
      	// 如果是String类型跳过检查
        if ((c = x.getClass()) == String.class) // bypass checks
            return c;
      	// 获取类的接口类型
        if ((ts = c.getGenericInterfaces()) != null) {
            for (int i = 0; i < ts.length; ++i) {
              	// 如果接口实现了Comparable 并且 泛型的类型就是c 那么返回
                if (((t = ts[i]) instanceof ParameterizedType) &&
                    ((p = (ParameterizedType)t).getRawType() ==
                     Comparable.class) &&
                    (as = p.getActualTypeArguments()) != null &&
                    as.length == 1 && as[0] == c) // type arg is c
                    return c;
            }
        }
    }
    return null;
}


/**
 * Returns k.compareTo(x) if x matches kc (k's screened comparable
 * class), else 0.
 */
@SuppressWarnings({"rawtypes","unchecked"}) // for cast to Comparable
static int compareComparables(Class<?> kc, Object k, Object x) {
  return (x == null || x.getClass() != kc ? 0 :
          ((Comparable)k).compareTo(x));
}

```



### Type 和 Class 的对比



Class 是对数据类型的描述，描述对象的基本组成

Type是Class的超集，意义是对Class类型的扩充。

例如：

```java
interface Comparable<Integer>{
}
```

该类的Class为java.lang.Comparable

但作为Type为java.lang.Comparable<java.lang.Integer> 

扩展了该类的泛型的信息

