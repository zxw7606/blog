---
title: Mysql的行列转换
date: 2020-02-29 11:52:06
tags:
	- msql
	- sql
---



```sql
-- 数据

INSERT INTO `t_score`(name,subject,score) VALUES
         ('王海', '语文', 86),
        ('王海', '数学', 83),
        ('王海', '英语', 93),
        ('陶俊', '语文', 88),
        ('陶俊', '数学', 84),
        ('陶俊', '英语', 94),
        ('刘可', '语文', 80),
        ('刘可', '数学', 86),
        ('刘可', '英语', 88),
        ('李春', '语文', 89),
        ('李春', '数学', 80),
        ('李春', '英语', 87);
```





![1582963329046](mysql的行列转换/1582963329046.png)

```sql
-- 期望的结构
-- 思路: 将subject的每个选项添加到字段 
select 'name','语文','数学','英语'


-- sql
SELECT name,
SUM(IF(SUBJECT='语文',score,0)) '语文',
SUM(IF(SUBJECT='数学',score,0)) '数学',
SUM(IF(SUBJECT='英语',score,0)) '英语'
FROM t_score GROUP BY NAME

-- 其中
-- "
-- SUM(IF(SUBJECT='语文',score,0)) '语文',
-- SUM(IF(SUBJECT='数学',score,0)) '数学',
-- SUM(IF(SUBJECT='英语',score,0)) '英语'
-- "
-- 是对每个数据SUBJECT字段判断3次，最后展示出3个字段,其中只有一个字段是真正的值,其他都是0
```


![1582963877515](D:\document\GitHub\blog\source\_posts\mysql的行列转换\1582963877515.png)

