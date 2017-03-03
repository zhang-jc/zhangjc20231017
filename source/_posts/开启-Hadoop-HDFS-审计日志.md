title: 开启 Hadoop HDFS 审计日志
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-03-03 11:32:35
---

1. etc/hadoop/log4j.properties

修改 etc/hadoop/log4j.properties 中 hdfs audit logging 部分，将以下配置：

    hdfs.audit.logger=INFO,NullAppender

改为：

    hdfs.audit.logger=INFO,RFAAUDIT

<!-- more -->

2. etc/hadoop/hadoop-env.sh

找到 etc/hadoop/hadoop-env.sh 文件中 HADOOP_NAMENODE_OPTS 配置部分，将原配置：

    export HADOOP_NAMENODE_OPTS="-Dhadoop.security.logger=${HADOOP_SECURITY_LOGGER:-INFO,RFAS} -Dhdfs.audit.logger=${HDFS_AUDIT_LOGGER:-INFO,NullAppender} $HADOOP_NAMENODE_OPTS"

修改为：

    export HADOOP_NAMENODE_OPTS="-Dhadoop.security.logger=${HADOOP_SECURITY_LOGGER:-INFO,RFAS} $HADOOP_NAMENODE_OPTS"

删除 -Dhdfs.audit.logger=${HDFS_AUDIT_LOGGER:-INFO,NullAppender}，使用 log4j.properties 中的配置。