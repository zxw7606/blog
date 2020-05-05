---
thumbnail: /images/linux.jpg
title: Linux中的系统文件
date: 2020-05-05 12:57:25
tags: [C,Linux,操作系统]
categories: [学习,笔记]
widgets: 
  - type: recent_posts
    position: right
---




### 权限文件

![](https://panjier0409.github.io/tuchuang/img/20200505170216.png)

<!-- more -->



### 其他设备文件

![](https://panjier0409.github.io/tuchuang/img/20200505170156.png)

### 账户记录文件

记录用户登录的信息，退出后在utmp中删除指定用户，wtmp不会

`/var/run/utmp`和`var/log/wtmp`

### UNAME 函数

查看系统信息，其中nodename为网络名

![](https://panjier0409.github.io/tuchuang/img/20200505170816.png?)

### 时间格式化格式

![](https://panjier0409.github.io/tuchuang/img/20200505165942.png)