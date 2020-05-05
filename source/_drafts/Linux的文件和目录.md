---
thumbnail: /images/linux.jpg
title: Linux的文件和目录
date: 2020-05-03 14:57:58
categories: [学习]
tags: [C,操作系统]
widgets: 
  - type: recent_posts
    position: right
---



## 文件类型

1. **普通文件**
2. **目录文件**
3. 块特殊文件（类似光驱设备文件，效果就是固定缓存快读写）
4. 字符特殊文件（像我们的终端就是这种，效果就是可以读写的缓存块大小随意）
5. **FIFO 管道**（效果就是不保存内容，写入进去必须有一个进程读取）
6. **套接字文件socket（用于进程间的通信）**
7. 符号链接文件



<!-- more -->



```c
// 文件属性
struct stat
  {
    __dev_t st_dev;		/* Device. 设备类型 */
    __mode_t st_mode;			/* File mode. 文件模式，即文件类型 以及权限 */
    __nlink_t st_nlink;			/* Link count.  链接数量 */
    __uid_t st_uid;		/* User ID of the file's owner.	*/
    __gid_t st_gid;		/* Group ID of the file's group.*/
    __off64_t st_size;			/* Size of file, in bytes.  */
    __blksize_t st_blksize;	/* Optimal block size for I/O.  */
    __blkcnt64_t st_blocks;		/* Number 512-byte blocks allocated. */
    struct timespec st_atim;		/* Time of last access. 描述文件最后被访问的时间 比如 read */
    struct timespec st_mtim;		/* Time of last modification.  描述最后文件被更改的时间 比如 write */
    struct timespec st_ctim;		/* Time of last status change.  描述 i节点被修改的最后时间 比如chown chmod*/
    __ino64_t st_ino;			/* File serial number.	*/
  };
```





## 什么是流

即对文件I/O，内存I/O的数据结构模型

### IO的缓存类型

1. 全缓冲比如文件
2. 行缓冲比如终端设备
3. 不缓冲比如错误描述符输出



### 输入函数

```c
// 宏
int getc(File *fp);
// 函数
int fgetc(File *fp);
// 等同getc(stdin)
int getchar(void);
// 压字符到流
int ungetc(int c, FILE *fp);

/* 行IO */

//读取n-1个字符直到遇到换行，输出到buf中
char *fgets(char *restrict buf, int n, FILE *restrict fp);
// 从标准输入读取数据到buf中，不推荐使用，无法预料读入的数据大小
char *fgets(char *buf);

/*二进制*/
```

### 格式化输入

`%[*][fldwidth][m][lenmodifier]convtype`

```c
scanf("%*d%s",buf); // 输入 3dddd
printf("%s",buf); // 输入 dddd,所以*代表忽略
```



### 输出函数

```c
// 宏
int pubc(File *fp);
// 函数
int fputc(File *fp);

int putchar(void);

/* 行IO */
// 函数 不提供换行
char *fputs(const char *restrict str,FILE *restrict fp);
// 宏 提供换行 不安全
char *puts(const char *str);

/*二进制*/

// 从ptr中写入size大小的数据 nobj个 到 fp中。
size_t fwrite(const void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp);
```

### 格式化输出

`%[flags][fldwidth][precision][lenmodifier]convtype`

| 标志   | 说明                                           |
| :----- | :--------------------------------------------- |
| ‘      | （撇号）将整数按千位分组字符                   |
| -      | 在字段内左对齐输出                             |
| +      | 总是显示带符号转换的正负号                     |
| (空格) | 如果第一个字符不是正负号，则在其前加入一个空格 |
| #      | 指定另一种转换形式                             |
| 0      | 添加前导0（而非空格）进行填充                  |

```c
    printf("% d\n",5);
    printf("% d\n",-5);
    printf("%+d\n",5);
    printf("%d\n",-5);
    printf("%10s\n","hello");
    printf("%-10s\n","hello");
    printf("%#x\n","hello");
    printf("%#X\n","hello");
    printf("%+e\n",5.13616156);
    printf("%010s\n","hello"); // 这里不知道为什么前面的0出不来

/*
     5
    -5
    +5
    -5
         hello
    hello     
    0x555547f6
    0X555547F6
    +5.136162e+00
         hello
*/
```





### 定位

```c
// 返回当前文件位置
long ftell(FILE *fp);

// 设置文件的位置
int fseek(FILE *fp, long offset, int whence);

```

