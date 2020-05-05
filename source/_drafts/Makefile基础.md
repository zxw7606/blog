---
thumbnail: /images/.jpeg
title: Makefile基础
date: 2020-04-26 22:13:22
categories: [学习]
tags: [C,C++]
---



### makefile基本语法

#### 案例1
```makefile
theLabel : file1.o file2.o      # 制作目标
cc -o file1.o file2.o           # 编译指令
```

之后输入指令：`make compile`或者`make`,将file1，file2编译成可执行文件

**makefile中的含义**

制作目标:：`theLabel` 指的是target 也就是一个目标文件，可以是 Object File，也可以是执行文件。还可以是一个 标签（Label）。这里很明显是标签，
编译指令：冒号后面的是所需的依赖。

编译指令：换行后面的命令是编译执行命令

**缺点** 
我们都知道，编译器会将.c文件会被编译成.o目标文件，该指令固定读取两个.o文件，如果.c源文件存在，他也不会读取重新编译。因此比较死板。

#### 案例2

```makefile
main.o: main.c hello1.c hello2.c # 制作目标
hello1.o: hello1.c               # 制作目标
hello2.o: hello2.c               # 制作目标
.PHONY: clean                    # 伪元素
clean: 
	rm -f *.o
```

**makefile中的含义**
制作目标：这里有多个制作目标，由此可见，该目标现在为一个目标文件，而不是一个标签，该.c文件会被编译为相应.o文件
.PHONY：如果我们将.PHONY 随便写成一个比如`abc`,那么编译器会误以为生成`abc`文件需要`一个clean`依赖

```bash
cxz@master:~/Cstu/mkfile$ touch clean
cxz@master:~/Cstu/mkfile$ make clean
make: “clean”已是最新。

```


**优点** 
当我们`make`之后，再次`make`，如果.c源代码没有发生改变，那么编译器将省略编译步骤。

**缺点**
每个文件都要写一个类似的对应关系条目


#### 其他可选指令

选择特定的makefile文件：`make -f Make.Linux`
makefile中引用makefile：`include foo.make *.mk $(bar)`
搜寻相关文件：`vpath %.h ../headers `,含义是搜寻../headers下的所有以.h结尾的头文件