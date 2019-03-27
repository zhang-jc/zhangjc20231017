title: Hive MetaException Invalid partition key & values
date: 2019-03-27 09:27:54
tags:
- Hive
categories:
- 大数据
- Hive
---
在 drop 某个 Table 的时候报了以下异常信息：

    hive> drop table temp.temp_log_day;
    FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask.     MetaException(message:Invalid partition key & values; keys [dt, device_type, province_alias, hour, ], values [20180312, ])

<!-- more -->

原因是在 Hive 的元数据库中记录该 Table 还有未 drop 掉的 Partition，但是在 HDFS 上已经不存在该 Partition 的路径了。按照提示信息先 drop 对应的 Partition。drop Partition 的时候会提示具体的路径信息：

    hive> ALTER TABLE temp.temp_log_day DROP PARTITION (dt='20180312');
    FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. Failed to delete parent: File does not exist: /user/hive/warehouse/temp.db/temp_log_day/dt=20180312/device_type=box/province_alias=00
	    at org.apache.hadoop.hdfs.server.namenode.FSDirStatAndListingOp.getContentSummaryInt(FSDirStatAndListingOp.java:492)
	    at org.apache.hadoop.hdfs.server.namenode.FSDirStatAndListingOp.getContentSummary(FSDirStatAndListingOp.java:139)
	    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.getContentSummary(FSNamesystem.java:3928)
	    at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.getContentSummary(NameNodeRpcServer.java:1200)
	    at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.getContentSummary(ClientNamenodeProtocolServerSideTranslatorPB.java:877)
	    at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
	    at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:616)
	    at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
	    at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2049)
	    at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2045)
	    at java.security.AccessController.doPrivileged(Native Method)
	    at javax.security.auth.Subject.doAs(Subject.java:422)
	    at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1698)
	    at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2045)

先在 HDFS 上创建对应的目录：

    $ hadoop fs -mkdir -p /user/hive/warehouse/temp.db/temp_log_day/dt=20180312/device_type=box/province_alias=00

再次执行 drop 操作：

    hive> drop table temp.temp_log_day;
    Moved: 'hdfs://ycluster/user/hive/warehouse/temp.db/temp_log_day' to trash at: hdfs://ycluster/user/test/.Trash/Current
    OK
