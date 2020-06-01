---
thumbnail: /images/mysql.jpg
title: Mysql数据库的备份与恢复
widgets:
  - type: recent_posts
    position: right
date: 2020-05-29 14:09:53
tags: mysql,数据库
categories: 学习
---

## 物理备份
### innobackupex备份

1. 备份数据库到指定目录：第一次备份采用全量备份：`innobackupex --user=backuser  --password=123456 --no-timestamp /var/backups/mysql [--databases]`,`databases`用空格分隔，之后的备份采用增量备份`innobackupex --user=backuser --password=123456 --no-timestamp --incremental /var/backups/mysql-icr-2020-05-29 --incremental-basedir=/var/backups/mysql`
2. 回滚备份文件中未提交的事务，同步备份文件已经提交的事务`innobackupex --apply-log /var/backups/mysql`
3. 停止mysql
4. 删除`/var/lib/mysql/`下的所有文件
5. 恢复数据：`innobackupex --copy-back /var/backups/mysql`,默认恢复数据文件到`/var/lib/mysql/`中
6. 修正`/var/lib/mysql/`权限

**常用的选项**
```text
--apply-log：该选项表示同xtrabackup的--prepare参数,一般情况下,在备份完成后，数据尚且不能用于恢复操作，因为备份的数据中可能会包含尚未提交的事务或已经提交但尚未同步至数据文件中的事务。因此，此时数据 文件仍处理不一致状态。--apply-log的作用是通过回滚未提交的事务及同步已经提交的事务至数据文件使数据文件处于一致性状态。
--use-memory：该选项表示和--apply-log选项一起使用，prepare 备份的时候，xtrabackup做crash recovery分配的内存大小，单位字节。也可(1MB,1M,1G,1GB)，推荐1G。
--defaults-file：该选项指定了从哪个文件读取MySQL配置，必须放在命令行第一个选项的位置。
--export：这个选项表示开启可导出单独的表之后再导入其他Mysql中。
--redo-only：这个选项在prepare base full backup，往其中merge增量备份（但不包括最后一个）时候使用。
```
<!-- more -->

## 逻辑备份

### 采用MYSQL的binlog(二进制日志文件备份)

1. 配置`my.cnf`

```text
# 集群数量，设置为单节点 日志文件不仅可以用来恢复数据，还可以在主从数据库中同步数据
server-id               = 1
# 二进制日志文件的存储目录/var/log/mysql/
log_bin                 = /var/log/mysql/mysql-bin.log
# 日志的删除有效时间
expire_logs_days        = 10
# 每个日志文件的最大块大小
max_binlog_size   = 100M
```

1. 全量数据备份`mysqldump -h132.72.192.432 -P3307 -uroot -p8888 [数据库名称] > bak.sql;`
2. 恢复数据的节点区间：`show binlog events in 'mysql-bin.000005'`，区间可以执行sql命令`show binlog events in 'mysql-bin.000005'`，或者执行shell命令`mysqlbinlog --start-position=219 --stop-position=695 /var/log/mysql/mysql-bin.000003 | mysql -uroot -p123456`查看

