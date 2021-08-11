---
title: Hive元数据MySQL编码导致Sqoop导入错误
date: 2021-08-10 16:10:28
tags:
- Hive
- 大数据
- Sqoop
- MySQL
categories:
- 大数据
- Hive
---

错误信息如下;

```Log
21/07/20 11:09:06 INFO mapreduce.Job:  map 100% reduce 0%
21/07/20 11:09:32 INFO mapreduce.Job: Job job_1593052953461_28645057 failed with state FAILED due to: Job commit failed: org.apache.hive.hcatalog.common.HCatException : 2006 : Error adding partition to metastore. Cause : MetaException(message:One or more instances could not be made persistent)
	at org.apache.hive.hcatalog.mapreduce.FileOutputCommitterContainer.registerPartitions(FileOutputCommitterContainer.java:966)
	at org.apache.hive.hcatalog.mapreduce.FileOutputCommitterContainer.commitJob(FileOutputCommitterContainer.java:250)
	at org.apache.hadoop.mapreduce.v2.app.commit.CommitterEventHandler$EventProcessor.handleJobCommit(CommitterEventHandler.java:285)
	at org.apache.hadoop.mapreduce.v2.app.commit.CommitterEventHandler$EventProcessor.run(CommitterEventHandler.java:237)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:748)
Caused by: MetaException(message:One or more instances could not be made persistent)
	at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:203)
	at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invoke(RetryingHMSHandler.java:99)
	at com.sun.proxy.$Proxy86.add_partitions(Unknown Source)
	at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.add_partitions(HiveMetaStoreClient.java:608)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.invoke(RetryingMetaStoreClient.java:150)
	at com.sun.proxy.$Proxy87.add_partitions(Unknown Source)
	at org.apache.hive.hcatalog.mapreduce.FileOutputCommitterContainer.registerPartitions(FileOutputCommitterContainer.java:920)
	... 6 more
Caused by: javax.jdo.JDOUserException: One or more instances could not be made persistent
NestedThrowables:
org.datanucleus.exceptions.NucleusDataStoreException: Put request failed : INSERT INTO `PARTITION_PARAMS` (`PARAM_VALUE`,`PART_ID`,`PARAM_KEY`) VALUES (?,?,?) 
	at org.datanucleus.api.jdo.JDOPersistenceManager.makePersistentAll(JDOPersistenceManager.java:775)
	at org.apache.hadoop.hive.metastore.ObjectStore.addPartitions(ObjectStore.java:1774)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.hadoop.hive.metastore.RawStoreProxy.invoke(RawStoreProxy.java:101)
	at com.sun.proxy.$Proxy84.addPartitions(Unknown Source)
	at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.add_partitions_core(HiveMetaStore.java:2365)
	at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.add_partitions(HiveMetaStore.java:2424)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:140)
	... 16 more
Caused by: org.datanucleus.exceptions.NucleusDataStoreException: Put request failed : INSERT INTO `PARTITION_PARAMS` (`PARAM_VALUE`,`PART_ID`,`PARAM_KEY`) VALUES (?,?,?) 
	at org.datanucleus.store.rdbms.scostore.JoinMapStore.putAll(JoinMapStore.java:233)
	at org.datanucleus.store.rdbms.mapping.java.MapMapping.postInsert(MapMapping.java:135)
	at org.datanucleus.store.rdbms.request.InsertRequest.execute(InsertRequest.java:522)
	at org.datanucleus.store.rdbms.RDBMSPersistenceHandler.insertObjectInTable(RDBMSPersistenceHandler.java:162)
	at org.datanucleus.store.rdbms.RDBMSPersistenceHandler.insertObject(RDBMSPersistenceHandler.java:138)
	at org.datanucleus.state.StateManagerImpl.internalMakePersistent(StateManagerImpl.java:3363)
	at org.datanucleus.state.StateManagerImpl.makePersistent(StateManagerImpl.java:3339)
	at org.datanucleus.ExecutionContextImpl.persistObjectInternal(ExecutionContextImpl.java:2079)
	at org.datanucleus.ExecutionContextImpl.persistObjectWork(ExecutionContextImpl.java:1922)
	at org.datanucleus.ExecutionContextImpl.persistObjects(ExecutionContextImpl.java:1867)
	at org.datanucleus.ExecutionContextThreadedImpl.persistObjects(ExecutionContextThreadedImpl.java:231)
	at org.datanucleus.api.jdo.JDOPersistenceManager.makePersistentAll(JDOPersistenceManager.java:764)
	... 30 more
Caused by: org.datanucleus.store.rdbms.exceptions.MappedDatastoreException: INSERT INTO `PARTITION_PARAMS` (`PARAM_VALUE`,`PART_ID`,`PARAM_KEY`) VALUES (?,?,?) 
	at org.datanucleus.store.rdbms.scostore.JoinMapStore.internalPut(JoinMapStore.java:1056)
	at org.datanucleus.store.rdbms.scostore.JoinMapStore.putAll(JoinMapStore.java:223)
	... 41 more
Caused by: java.sql.SQLException: Incorrect string value: '\xE9\xA2\x91\xE9\x81\x93...' for column 'PARAM_VALUE' at row 1
	at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:964)
	at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3973)
	at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3909)
	at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:2527)
	at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2680)
	at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2490)
	at com.mysql.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:1858)
	at com.mysql.jdbc.PreparedStatement.executeUpdateInternal(PreparedStatement.java:2079)
	at com.mysql.jdbc.PreparedStatement.executeUpdateInternal(PreparedStatement.java:2013)
	at com.mysql.jdbc.PreparedStatement.executeLargeUpdate(PreparedStatement.java:5104)
	at com.mysql.jdbc.PreparedStatement.executeUpdate(PreparedStatement.java:1998)
	at com.jolbox.bonecp.PreparedStatementHandle.executeUpdate(PreparedStatementHandle.java:205)
	at org.datanucleus.store.rdbms.ParamLoggingPreparedStatement.executeUpdate(ParamLoggingPreparedStatement.java:393)
	at org.datanucleus.store.rdbms.SQLController.executeStatementUpdate(SQLController.java:431)
	at org.datanucleus.store.rdbms.scostore.JoinMapStore.internalPut(JoinMapStore.java:1047)
	... 42 more


21/07/20 11:09:32 INFO mapreduce.Job: Counters: 31
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=1367592
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=476
		HDFS: Number of bytes written=67625
		HDFS: Number of read operations=16
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=8
	Job Counters 
		Killed map tasks=1
		Launched map tasks=5
```

将异常信息中提示的字符串“\xE9\xA2\x91\xE9\x81\x93...”解码后为“频道”，因此怀疑是编码导致的问题。查看Hive表定义：

```SQL
hive> show create table tbl_test;
OK
CREATE TABLE `tbl_test`(
  `channel_id` string, 
  `uuid` string, 
  `channel_name` string, 
  `channel_status` string, 
  `tv_channel_logo` string, 
  `phone_channel_logo` string, 
  `definition_type` string COMMENT '频道码率', 
  `create_datetime` string, 
  `update_datetime` string)
PARTITIONED BY ( 
  `province_alias` string, 
  `dt` string)
ROW FORMAT SERDE 
  'org.apache.hadoop.hive.ql.io.orc.OrcSerde' 
STORED AS INPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.orc.OrcInputFormat' 
OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.orc.OrcOutputFormat'
LOCATION
  'hdfs://ycluster/user/hive/warehouse/ods.db/cos_channel_v2_bak_20210630'
TBLPROPERTIES (
  'last_modified_by'='data-platform', 
  'last_modified_time'='1628153252', 
  'spark.sql.create.version'='2.2 or prior', 
  'spark.sql.sources.schema.numPartCols'='2', 
  'spark.sql.sources.schema.numParts'='1', 
  'spark.sql.sources.schema.part.0'='{\"type\":\"struct\",\"fields\":[{\"name\":\"channel_id\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"uuid\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"channel_name\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"channel_status\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"tv_channel_logo\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"phone_channel_logo\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"definition_type\",\"type\":\"string\",\"nullable\":true,\"metadata\":{\"\u9891\u9053\u7801\u7387\"}},{\"name\":\"create_datetime\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"update_datetime\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"province_alias\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}},{\"name\":\"dt\",\"type\":\"string\",\"nullable\":true,\"metadata\":{}}]}', 
  'spark.sql.sources.schema.partCol.0'='province_alias', 
  'spark.sql.sources.schema.partCol.1'='dt', 
  'transient_lastDdlTime'='1628650471')
Time taken: 0.041 seconds, Fetched: 31 row(s)
```

发现在Table的属性中“spark.sql.sources.schema.part.0”中有Union Code编码，而我们的环境都是UTF8编码。跟踪源代码发现在添加分区时会将表的属性信息依次记录到分区的参数中。因此检查MySQL表字段编码：![partition params encoding](/images/20210811/partition_params_encoding.png)

修改表字段编码后问题解决。参考SQL如下：

```SQL
ALTER TABLE `hive`.`partition_params` CHANGE COLUMN `PARAM_VALUE` `PARAM_VALUE` VARCHAR(4000) CHARACTER SET 'utf8' NULL DEFAULT NULL;
```