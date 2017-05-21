title: HDFS NameNode HA sshfence 端口
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-05-21 08:17:18
---

当 HDFS NameNode 发生 Failover 时，为了防止发生“脑裂”现象，需要对原 Active 的 NameNode 采用防护措施。当两个 NameNode 节点直接互相 SSH 使用非默认端口 22 时，需要在配置中指定使用的端口。例如，使用端口 2222，则配置如下：

    <property>
      <name>dfs.ha.fencing.methods</name>
      <value>sshfence(hadoop:2222)</value>
    </property>

其中，小括号中的 hadoop 为 SSH 的用户名，2222 为端口号。采用 SSH 方式需要先打通两台 NameNode 间互相 SSH 的白名单。
