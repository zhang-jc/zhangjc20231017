title: 'MySQL 表分区修改操作不支持 IF [NOT] EXISTS'
tags:
  - MySQL
categories:
  - 数据库
  - MySQL
date: 2016-05-19 22:38:42
---

### MySQL 删除表分区不支持 IF [NOT] EXISTS

最近的工作中需要用到 MySQL 的分区表。表根据每天的日期分区，如：20160518、20160519 等。需要支持数据重新写入分区，即如果对应的分区下已经存在数据，则先清理再写入。IF [NOT] EXISTS 是一种判定表是否存在简便方式，非常适合这种场景。但查看了 MySQL 的官方文档，PARTITION 的添加、删除操作不支持该操作。

<!-- more -->

[ALTER TABLE Partition Operations](http://dev.mysql.com/doc/refman/5.7/en/alter-table-partition-operations.html)

![ADD PARTITION and DROP PARTITION do not currently support IF [NOT] EXISTS.](/uploads/20160518/mysql-alter-partition.png)

### 解决方法

通过 MySQL 的 INFORMATION_SCHEMA 库下的 [PARTITIONS](http://dev.mysql.com/doc/refman/5.7/en/partitions-table.html) 表查询表分区信息确定分区是否存在。该表的使用见下面的例子：

    mysql> CREATE TABLE th (
        ->     c1 INT,
        ->     c2 VARCHAR(20)
        -> )
        -> PARTITION BY HASH(c1)
        -> PARTITIONS 2;
    Query OK, 0 rows affected (0.00 sec)

可以用下面的查询表 th 的分区情况：

    mysql> SELECT TABLE_NAME,PARTITION_NAME,TABLE_ROWS,AVG_ROW_LENGTH,DATA_LENGTH
         >   FROM INFORMATION_SCHEMA.PARTITIONS
         >   WHERE TABLE_SCHEMA = 'p' AND TABLE_NAME ='th';

结果如下：![mysql-information-schema-partitions](/uploads/20160518/mysql-information-schema-partitions.png)
