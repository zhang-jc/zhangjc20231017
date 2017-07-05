title: Sqoop 导入 MySQL 中含有回车换行符的数据
tags:
  - Sqoop
  - Hive
categories:
  - 大数据
  - Sqoop
date: 2017-07-05 09:24:44
---

MySQL 中的数据如下图：![MySQL 中带回车换行符的数据](/uploads/20170704/mysql-data.png)

<!-- more -->

检查 HDFS 上的目标文件内容可以看出，回车换行符位置的数据被截断了，导致数据列错位。![HDFS 问题数据](/uploads/20170704/hdfs-data.png)

Sqoop 提供了配置参数，在导入时丢弃掉数据的分隔符（\n，\r，\01）。

    --hive-drop-import-delims : Drops \n, \r, and \01 from string fields when importing to Hive. 
