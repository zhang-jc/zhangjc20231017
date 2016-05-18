title: 'MySQL 表分区修改操作不支持 IF [NOT] EXISTS'
tags:
- MySQL
- SQL
- PARTITION
categories:
- 数据库
- MySQL
---
最近的工作中需要用到 MySQL 的分区表。表根据每天的日期分区，如：20160518、20160519 等。需要支持数据重新写入分区，即如果对应的分区下已经存在数据，则先清理再写入。IF [NOT] EXISTS 是一种判定表是否存在简便方式，非常适合这种场景。但查看了 MySQL 的官方文档，PARTITION 的添加、删除操作不支持该操作。

[ALTER TABLE Partition Operations](http://dev.mysql.com/doc/refman/5.7/en/alter-table-partition-operations.html)

![ADD PARTITION and DROP PARTITION do not currently support IF [NOT] EXISTS.](/uploads/20160518/mysql-alter-partition.png)
