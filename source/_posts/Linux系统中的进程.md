---
thumbnail: /images/linux.jpg
title: Linux中的系统进程
date: 2020-05-05 17:22:37
tags: [C,Linux,操作系统]
categories: [学习,笔记]
widgets: 
  - type: recent_posts
    position: right
---





## 系统执行C程序流程图

![系统执行C程序流程图](https://panjier0409.github.io/tuchuang/img/20200505172647.png)



<!-- more -->



## 系统中的环境表

![系统中的环境表](https://panjier0409.github.io/tuchuang/img/20200505173049.png)



### C程序的存储空间

![](https://panjier0409.github.io/tuchuang/img/20200505184838.png)



### 终端设备登录流程

![终端设备登录流程](https://panjier0409.github.io/tuchuang/img/20200505215211.png)

其中getty程序将0,1,2文件描述符都设置到该设备上



![-sh的原因](https://panjier0409.github.io/tuchuang/img/20200505215516.png)



### 进程，进程组，会话之间的关系（控制终端会和会话是一对一的关系）

会话是一个或多个进程组的集合，会话是什么时候建立的呢，定义是说调用`setsid`函数的进程不是进程组的组长会建立一个进的会话，只有一种情况，当前进程组没有进程的时候，那就是login shell被分配一个新的uid，gid，pid的时候
![](https://panjier0409.github.io/tuchuang/img/20200505221250.png)


![会话，进程，进程组，控制终端的数据结构](https://panjier0409.github.io/tuchuang/img/20200505234107.png)



## 前后台进程组（作业）与终端的关系

![](https://panjier0409.github.io/tuchuang/img/20200505222948.png)

注意：后台进程是无法接受终端输入的字符，只有前台进程可以，比如输入`cat &`获取终端输入，但是他是一个后台进程，所以收到`SIGTTIN`信号后它会挂起。除非把它转换成前台进程。


## 管道和终端之间的关系

![](https://panjier0409.github.io/tuchuang/img/20200505223441.png)

## 孤儿进程组（正常操作，并非异常）
即父进程停止，子进程继续


## 父进程结束， 子进程未结束的状态

孤儿进程的定义就是该进程组的每一个父进程都在另一个会话（这种情况感觉好难）

![模拟孤儿进程](https://panjier0409.github.io/tuchuang/img/20200505232938.png)

可想而知，系统是知道孤儿进程存在，并且有着明确的处理方式

## 待续