---
thumbnail: /images/docker.jpg
title: docker数据卷(Volume)
date: 2020-03-16 23:05:32
tags:
    - docker
categories:
    - [阅读笔记]
---


### 简介

> 想要了解Docker Volume，首先我们需要知道Docker的文件系统是如何工作的。**Docker镜像是由多个文件系统（只读层）叠加而成。** 当我们启动一个容器的时候，Docker会加载只读镜像层并在其上（译者注：镜像栈顶部）添加一个读写层。如果运行中的容器修改了现有的一个已经存在的文件，**那该文件将会从读写层下面的只读层复制到读写层（新建一个读写层，可能原理与git的文件系统相似）**，该文件的只读版本仍然存在，只是已经被读写层中该文件的副本所隐藏。当删除Docker容器，并通过该镜像重新启动时，之前的更改将会丢失。在Docker中，只读层及在顶部的读写层的组合被称为Union File System（联合文件系统）。
>>为了能够保存（持久化）数据以及共享容器间的数据，Docker提出了Volume的概念。简单来说，Volume就是目录或者文件，它可以绕过默认的联合文件系统，而以正常的文件或者目录的形式存在于宿主机上。


### 个人理解：

一旦容器指定一个Volume，该Volume将会和宿主机的一个**文件/目录**绑定，即宿主文件系统的一个目录挂载到容器内部的目录上。这样一来如果这个容器提交为新的镜像，那么仅仅只有没被挂载的文件/目录将会被提交，而容器指定的Volume内部的数据将不会被保存。虽然不会被保存，但是这个Volume将会永久的存在于宿主机中

<!-- more -->

### 创建Volume的方法

1. `docker run -v /etc/mysql/`，含义是将宿主机中一个随机生成的目录挂载到容器内部的/etc/mysql/上。具体的目录地址可以在容器启动之后`docker inspect [container-name]`查看具体的地址。
2. `docker run -v xxx/xxx:/etc/mysql/`，含义是将宿主机中一个指定的目录挂载到容器内部的/etc/mysql/上
3. Dockerfile中编写`VOLUME:['dir/a','dir/b']`,含义是分别将宿主机中随机生成的目录挂载到容器内部的dir/a上和dir/b上。


### 备份volume

1. 我认为直接压缩目录挺好的
2. `docker run --rm --volumes-from [container-name] -v $(pwd):/backup ubuntu tar cf /backup/data.tar /data`，其中`--rm`指的是这个容器跑完之后就被删掉，`--volumes-from [container-name]`将指定的容器的Volume全部与这个即将运行的容器共享，比如 a镜像->{'host_a':'container_a'}, b镜像->{'host_b':'container_b'},执行 `docker run a --volumes-from b`, 那么 b下的container_a绑定host_a的数据