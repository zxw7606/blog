---
title: Linux环境变量和权限
date: 2019-11-24 11:01:16
tags: [C,操作系统]
---





## 环境变量

### 全局环境变量

``作用范围``: 全局环境变量对于shell会话和所有生成的子shell 



``查看方式``：

```bash
[cxz@os2 ~]$ env
```



### 局部环境变量

``作用范围``: 局部环境变量只能在定义它们的进程中可见 

``查看方式``：

```shell
# set命令会显示为某个特定进程设置的所有环境变量，包括局部变量、全局变量以及用户定义变量
[cxz@os2 ~]$ set
```



### 系统读取环境变量

```shell
# 系统默认登陆shell都会读取
/etc/profile
# 登陆shell（用户登陆的配置）
$HOME/.bash_profile 
$HOME/.bashrc # 交互式shell(就是命令行输入bash的shell)

# 登陆shell 配置
$HOME/.bash_login
$HOME/.profile 
```



例如登陆cxz

```shell
[cxz@os2 ~]$ ssh 127.0.0.1
cxz@127.0.0.1's password: 
Last login: Sun Nov 24 11:39:25 2019
hello /etc/profile
hello .bash_profile
hello .bashrc

```

交互式（包括右键桌面 打开终端）

```shell
[cxz@os2 ~]$ bash
hello .bashrc
```





## 权限

**umask**

```shell
[cxz@os2 ~]$ umask 
0002 # 其他用户去除一个写权限（2），当前用户和当前组不变
[cxz@os2 ~]$ umask -S
u=rwx,g=rwx,o=rx
```





``设置用户ID(SUID)``: 当文件被用户使用时，程序会以**文件属主**的权限运行。

``设置组ID(SGID)``:对文件来说，程序会以**文件属组**的权限运行;对目录来说，目录中 

创建的新文件会以目录的默认属组作为默认属组。 

``粘着位``: 进程结束后文件还驻留(粘着)在内存中。 





```shell
[root@os2 cxz]# ls -al /usr/bin/docker
-rwxr-xr-x. 1 root root 735 9月  13 22:25 /usr/bin/docker
[root@os2 cxz]# chmod u+s /usr/bin/docker
[root@os2 cxz]# ls -al /usr/bin/docker
-rwsr-xr-x. 1 root root 735 9月  13 22:25 /usr/bin/docker

```

