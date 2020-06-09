---
#thumbnail: /images/.jpeg
title: MongoDB基本知识和使用
widgets:
  - type: recent_posts
    position: right
  - type: toc
    position: right
toc: true
date: 2020-06-09 16:13:43
tags: MongoDB, 数据库
categories: [学习, 笔记]
---

### MongoDB的数据类型

| 名称 | 类型   |  作用   |
| :--- | :----- | --- |
|`Object ID` |Documents 自生成的 _id|
|`String`| 字符串|必须是utf-8|
|`Boolean`|布尔值|true 或者false|
|`Integer`|整数 |`N/A`|
|`Double`|浮点数|`N/A`|
|`Arrays`|数组或者列表|`N/A`|
|`Object`|`N/A`|
|`Null`|空数据类型|`N/A`|
|`Timestamp`|时间戳|`N/A`|
|`Date`|存储当前日期或时间unix时|

<!-- more -->
### MongoDB快速使用

1. MongoDB数据库:`show dbs`
2. 使用MongoDB数据库:`use xxx`
3. 创建一个集合(类似表):`db.createCollection("xxx")
4. 插入数据`db.getCollection("mytest").insert({
         "name": "notebook",
         "qty": 50,
         "rating": [ { "score": 8 }, { "score": 9 } ],
         "size": { "height": 11, "width": 8.5, "unit": "in" },
         "status": "A",
         "tags": [ "college-ruled", "perforated"]
        })
`,返回结果`WriteResult({ "nInserted" : 1, "writeConcernError" : [ ] })`
5. 查询数据`db.mytest.find({qty: {$gt: 49}}, {name: 1,_id: 0}).limit(4)`,只返回name字段,$gte（大于或等于）、$lt（小于）、$lte（小于或等于）
6. 删除数据`db.col.remove({qty: {$gt: 49})`

间格式| (我们一般不用这个Date类型,时间戳可以秒杀一切时间类型)|