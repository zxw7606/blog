---
title: TensorFlow2快速开始
date: 2019-12-14 15:02:37
tags:
	- 深度学习
	- python

---



## 基本数据类型



### 基本类型

```python
tf1 = tf.constant([[3,4], [1,2]])
tf1,tf1.ndim

(<tf.Tensor: id=63, shape=(2, 2), dtype=int32, numpy=
 array([[3, 4],
        [1, 2]], dtype=int32)>, 2)
```



```python
tf.zeros([1,1,2])
tf.zeros(tf1) # 从Tensor创建
tf.fill([1,1,2],0)
tf.random.normal([1,2,1],mean=均值, stddev=方差)
tf.random.uniform([1,2,1],minval=最小值,maxval=最大值)


```





### numpy转化



```python
np2 = np.arange(5)
tf2 = tf.convert_to_tensor(np2) # np2 可以为 普通数据类型
np2, tf2

(array([0, 1, 2, 3, 4]),
 <tf.Tensor: id=65, shape=(5,), dtype=int64, numpy=array([0, 1, 2, 3, 4])>)
```



### 类型转化



```python
b = tf.cast(a , dtype=tf.int) # 转化为int
```



### tf.Variable

```python
W = tf.Variable(b) # variable代表一个具有可自动求导特性的Tensor
```



