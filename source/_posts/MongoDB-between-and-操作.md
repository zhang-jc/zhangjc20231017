title: MongoDB between ... and ... 操作
tags:
  - MongoDB
categories:
  - 数据库
  - MongoDB
date: 2016-07-11 14:15:52
---


MongoDB 中类似 SQL 的 between and 操作可以采用如下语法：

    db.collection.find( { field: { $gt: value1, $lt: value2 } } );
