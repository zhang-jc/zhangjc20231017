---
title: hive cli OOM
date: 2021-09-27 11:20:57
tags:
- Hive
- 大数据
- Hadoop
categores:
- 大数据
- Hive
---

执行语句及异常信息如下：

```Shell
hive> ANALYZE TABLE table1 partition(dt='20210926', hour='13') compute statistics;
Exception in thread "main" java.lang.OutOfMemoryError: GC overhead limit exceeded
	at org.apache.thrift.protocol.TBinaryProtocol.readFieldBegin(TBinaryProtocol.java:245)
	at org.apache.hadoop.hive.metastore.api.FieldSchema$FieldSchemaStandardScheme.read(FieldSchema.java:487)
	at org.apache.hadoop.hive.metastore.api.FieldSchema$FieldSchemaStandardScheme.read(FieldSchema.java:480)
	at org.apache.hadoop.hive.metastore.api.FieldSchema.read(FieldSchema.java:414)
	at org.apache.hadoop.hive.metastore.api.StorageDescriptor$StorageDescriptorStandardScheme.read(StorageDescriptor.java:1299)
	at org.apache.hadoop.hive.metastore.api.StorageDescriptor$StorageDescriptorStandardScheme.read(StorageDescriptor.java:1278)
	at org.apache.hadoop.hive.metastore.api.StorageDescriptor.read(StorageDescriptor.java:1140)
	at org.apache.hadoop.hive.metastore.api.Partition$PartitionStandardScheme.read(Partition.java:1065)
	at org.apache.hadoop.hive.metastore.api.Partition$PartitionStandardScheme.read(Partition.java:1000)
	at org.apache.hadoop.hive.metastore.api.Partition.read(Partition.java:882)
	at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$get_partitions_result$get_partitions_resultStandardScheme.read(ThriftHiveMetastore.java)
	at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$get_partitions_result$get_partitions_resultStandardScheme.read(ThriftHiveMetastore.java)
	at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$get_partitions_result.read(ThriftHiveMetastore.java)
	at org.apache.thrift.TServiceClient.receiveBase(TServiceClient.java:86)
	at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Client.recv_get_partitions(ThriftHiveMetastore.java:2958)
	at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Client.get_partitions(ThriftHiveMetastore.java:2943)
	at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.listPartitions(HiveMetaStoreClient.java:1368)
	at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.listPartitions(HiveMetaStoreClient.java:1362)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.invoke(RetryingMetaStoreClient.java:212)
	at com.sun.proxy.$Proxy37.listPartitions(Unknown Source)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.hadoop.hive.metastore.HiveMetaStoreClient$SynchronizedHandler.invoke(HiveMetaStoreClient.java:2773)
	at com.sun.proxy.$Proxy37.listPartitions(Unknown Source)
	at org.apache.hadoop.hive.ql.metadata.Hive.getAllPartitionsOf(Hive.java:2981)
	at org.apache.hadoop.hive.ql.optimizer.ppr.PartitionPruner.getAllPartitions(PartitionPruner.java:481)
```

原因是Hive Cli进程内存不足，通过添加以下参数进行修改：

```Shell
export HADOOP_CLIENT_OPTS="-Xmx2g -Xms2g"
```