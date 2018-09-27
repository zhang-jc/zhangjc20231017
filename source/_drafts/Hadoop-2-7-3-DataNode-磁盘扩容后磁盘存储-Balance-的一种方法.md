title: Hadoop 2.7.3 DataNode 磁盘扩容后磁盘存储 Balance 的一种方法
tags:
- Hadoop
- HDFS
categories:
- 大数据
- Hadoop
---

Hadoop 2.7.3 DataNode 新加入磁盘后，数据不能按照磁盘进行 Balance。虽然 DataNode 整体存储负载降低了，但是存储负载很高的磁盘不会降低。这里采用的方法是将 DataNode 先 decommission，然后删除数据，再重新加入集群。

<!-- more -->

### DataNode Decommission

1. 在 NameNode 上，把需要 Decommission 的 DataNode 的机器名加入到 hdfs-site.xml 文件 dfs.hosts.exclude 配置项指定的文件中。配置项如下：

    <property>
      <name>dfs.hosts.exclude</name>
      <value>/etc/hadoop/conf/dfs.exclude</value>
    </property>
2. 用如下命令启动 Decommission：

    hdfs dfsadmin -refreshNodes


