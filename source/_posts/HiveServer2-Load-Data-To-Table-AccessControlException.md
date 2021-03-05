title: HiveServer2 Load Data To Table AccessControlException
date: 2021-03-05 15:54:29
tags:
- Hive
- Hadoop
- 大数据
categories:
- 大数据
- Hive
---

在用HiveServer2执行语句”load data inpath '/user/test/test.csv' into table dev.load_test_tbl;“时报错信息如下：

	Failed with exception org.apache.hadoop.security.AccessControlException: User does not belong to data-dev
		at org.apache.hadoop.hdfs.server.namenode.FSDirAttrOp.setOwner(FSDirAttrOp.java:86)
		at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.setOwner(FSNamesystem.java:1676)
		at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.setOwner(NameNodeRpcServer.java:703)
		at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.setOwner(ClientNamenodeProtocolServerSideTranslatorPB.java:464)
		at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
		at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:616)
		at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
		at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2049)
		at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2045)
		at java.security.AccessController.doPrivileged(Native Method)
		at javax.security.auth.Subject.doAs(Subject.java:422)
		at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1698)
		at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2045)
	
	FAILED: Operation cancelled
	
原因是Hive默认在Load数据时会修改HDFS上目的地下的数据文件与目的地目录的属主及组等权限信息一致，而提交任务的用户信息与目标目录的信息不一致，所以抛出如上异常。

解决方法：在hiveserver2-site.xml中增加以下配置：

    <property>
        <name>hive.warehouse.subdir.inherit.perms</name>
        <value>false</value>
    </property>
