title: 'HDFS getmerge: Operation not permitted'
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-05-31 21:16:50
---


在 hadoop fs -getmerge 时报了不被允许的错误。检查发现是因为权限条件不满足。getmerge 操作会对应数据文件在本地目录下生成一个隐藏的以 crc 结尾的文件。我遇到的场景是，用户 A 首先执行了以下命令：

    hadoop fs -getmerge /data/log_001.txt /data/logs/log_001.txt
    
此时，在本地目录 /data/logs/ 下会对应生成以下文件：

    -rw-r--r-- 1 userA data-platform    0 May 31 21:00 log_001.txt
    -rw-r--r-- 1 userA data-platform   16 May 31 20:56 .log_001.txt.crc
    
然而，当用户 B 再执行相同的命令时就会报 Operation not permitted 错误。原因是用户 B 没有删除 .log_001.txt.crc 文件的权限。
