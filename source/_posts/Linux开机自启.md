---
thumbnail: /images/.jpg
title: Linux开机自启
date: 2020-03-17 09:25:11
tags:
categories:
---
## **Linux开机自启**

### 通用方法 

编写目录下的`/etc/rc.local`文件,该文件软链接到`/etc/rc.d/rc.local`，添加执行权限`chmod +x /etc/rc.d/rc.local`

### centos方法(分版本)
查看系统版本 `cat /etc/redhat-release`,

#### 版本7：
查看系统中的服务`systemctl list-unit-files -a`对应的enbale即为启动服务，

##### 编写服务(.service)

配置文件存在于以下三个目录中
    1. /etc/systemd/system 存放系统启动的默认级别及启动的unit的软连接, 优先级最高
    2. /run/systemd/system 系统执行过程中产生的服务脚本, 优先级第二
    3. /usr/lib/systemd/system 存放系统上所有的启动文件, 优先级最低


```bash

[Unit]
Description= # 描述
Documentation= # 文档 例如：man:sshd(8) man:sshd_config(5) 
# Before=xxx.service 可选
After=network.target # 描述服务类别，表示本服务需要在network服务启动后在启动
Wants=sshd-keygen.service # 弱依赖,表示等号填写服务启动或失败,不影响此配置文件服务的启动
#Requires=服务.service 强依赖,表示等号填写服务启动或失败,那么此配置文件的服务将启动失败

[Service]
Type=forking #表示 fork()方式启动，此时父进程将会退出，子进程将成为主进程（后台运行）
# simple ExecStart字段启动的进程为主进程
# notify 启动结束后会发出通知信号，然后 Systemd 再启动其他服务


EnvironmentFile=/etc/sysconfig/sshd
PIDFile=/var/run/xxx.pid # pid文件 该文件存储了进程的Id

# ExecStartPre=启动服务之前执行的命令
ExecStart=/usr/sbin/sshd -D $OPTIONS # 系统启动脚本 注意绝对路径
# ExecStartPost=启动服务之后执行的命令
ExecReload=/bin/kill -HUP $MAINPID # 系统重启脚本
# ExecStop=停止服务时执行的命令
# ExecStopPost=停止服务之后执行的命令
KillMode=process # 只杀主进程
Restart=on-failure # 非正常退出时（退出状态码非0），包括被信号终止和超时
RestartSec=42s # 重启间隔

[Install]
# 描述需要在 multi-user.target 的系统快照之后执行
WantedBy=multi-user.target  

```
[参考链接](https://www.cnblogs.com/wutao666/p/9781567.html)


#### 版本6

##### 使用命令chkconfig设置开机启动

```bash

#chkconfig功能说明：检查，设置系统的各种服务语法： 
chkconfig [--add][--del][--list][系统服务] 或 
chkconfig [--level<等级代号>][系统服务][on/off/reset]
--add 添加服务
--del 删除服务
--list 查看各服务启动状态

# 系统服务是文件名 在 /etc/rc.d/init.d/
```