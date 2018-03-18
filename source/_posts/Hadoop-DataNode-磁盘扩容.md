title: Hadoop DataNode 磁盘扩容
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2018-03-18 11:06:56
---


DataNode 是支持热插拔磁盘的，所以磁盘扩容比较简单。磁盘格式化后挂载完毕。

<!-- more -->

修改 hdfs-site.xml 配置文件，将新增的磁盘目录加到配置中：

    <property>
      <name>dfs.datanode.data.dir</name>
      <value>/data0/hadoop/dfs/data,/data1/hadoop/dfs/data</value>
    </property>

修改 yarn-site.xml 配置文件，将新增的磁盘目录加到配置中：

    <property>
      <name>yarn.nodemanager.local-dirs</name>
      <value>/data0/hadoop/yarn/data,/data1/hadoop/yarn/data</value>
    </property>
    <property>
      <name>yarn.nodemanager.log-dirs</name>
      <value>/data0/hadoop/yarn/log,/data1/hadoop/yarn/log</value>
    </property>

配置修改完成后将配置文件分发到所有的节点。依次重启 DataNode 和 NodeManager。

如果只为 DataNode 新增磁盘，可以不用重启 DataNode，使用 reconfig 刷新配置即可：

    $ hdfs dfsadmin -reconfig datanode 192.168.72.2:50020 start
    Started reconfiguration task on DataNode 192.168.72.2:50020

查看重新配置执行状态的时候会有误报的错误信息，这个问题的跟踪地址：<https://issues.apache.org/jira/browse/HDFS-8582>。错误信息如下：

    $ hdfs dfsadmin -reconfig datanode 192.168.72.2:50020 status
    Reconfiguring status for DataNode[192.168.72.2:50020]: started at Thu Mar 08 17:56:10 CST 2018 and finished at Thu Mar 08 17:56:11 CST 2018.
    FAILED: Change property yarn.nodemanager.log-dirs
	From: "/data0/hadoop/yarn/log"
	To: "/data0/hadoop/yarn/log,/data1/hadoop/yarn/log"
	Error: Property yarn.nodemanager.log-dirs is not reconfigurable.
    FAILED: Change property rpc.engine.org.apache.hadoop.ipc.ProtocolMetaInfoPB
	From: "org.apache.hadoop.ipc.ProtobufRpcEngine"
	To: ""
	Error: Property rpc.engine.org.apache.hadoop.ipc.ProtocolMetaInfoPB is not reconfigurable.
    FAILED: Change property dfs.datanode.startup
	From: "REGULAR"
	To: ""
	Error: Property dfs.datanode.startup is not reconfigurable.
