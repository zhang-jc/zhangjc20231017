---
title: Spark SQL查询HBase表异常解决
date: 2022-04-02 17:06:42
tags:
- Spark
- HBase
- Hive
- KDE
---

# hbase-default.xml file seems to be for an older version

```Log
spark-sql>SELECT FROM test.test_hbase_table LIMIT 100;
java.lang.RuntimeException:java.lang.RuntimeException:hbase-default.xml file seems to be for an older version of HBase (1.2.3),this version is 2.0.0.3.0.1.0.187
        at org,apache.hadoop.hive.ql.metadata.Table.getStorageHandler(Table.java:292)
        at org.apache.spark.sql.hive.client.HiveClientImplsSanon funSgetTableOptions1ssanon funSapplys7.apply(HiveClientImpl.scala:407)
        at org.apache.spark.sql.hive.client.HiveClientImplsSanon funsgetTableOptions1ssanon funsapplys7.apply(HiveClientImpl.scala:374)
        at scala.Option.map(Option.scala:146)
        at org.apache.spark.sql.hive.client.HiveClientImplssanon funSgetTableOptions1.apply(HiveClientImpl.scala:374)
        at org.apache.spark.sql.hive.client.HiveClientImplssanon funSgetTableOptions1.apply(HiveClientImpl.scala:372)
        at org.apache.spark.sql.hive.client.HiveClientImplsSanonfunSwithHiveStates1.apply(HiveClientImpl.scala:281)
        at org.apache.spark.sql.hive.client.HiveClientImpl.liftedTreels1(HiveClientImpl.scala:219)
        at org.apache.spark.sql.hive.client.HiveClientImpl.retryLocked(HiveClientImpl.scala:218)
        at org.apache.spark.sql.hive.client.HiveClientImpl.withHiveState(HiveClientImpl.scala:264)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTableOption(HiveClientImpl.scala:372)
        at org.apache.spark.sql.hive.client.Hiveclientsclass.getTable(Hiveclient.scala:81)
        at org.apache.spark.sql.hive.client.HiveClientImpl.getTable(HiveClientImpl.scala:84)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getRawTable(HiveExternalCatalog.scala:118)
        at org.apache.spark.sql.hive.HiveExternalCatalogssanon funSgetTables1.apply(HiveExternalCatalog.scala:700)
        at org.apache.spark.sql.hive.HiveExternalCatalogssanon funSgetTables1.apply(HiveExternalCatalog.scala:700)
        at org.apache.spark.sql.hive.HiveExternalCatalog.withClient(HiveExternalCatalog.scala:97)
        at org.apache.spark.sql.hive.HiveExternalCatalog.getTable(HiveExternalCatalog.scala:699)
```

解决方法是在hbase-site.xml文件中添加如下配置：

```XML
<property>
    <name>hbase.defaults.for.version.skip</name>
    <value>true</value>
    <description>
    Set to true to skip the 'hbase.defaults.for.version' check. Setting this to true can be useful in contexts other than the other side of a maven generation; i.e. running in an ide. You'll want to set this boolean to true to avoid seeing the RuntimException complaint: "hbase-default.xml file seems to be for and old version of HBase (0.92.1), this version is X.X.X-SNAPSHOT"
    </description>
  </property>
```

注意，hbase-site.xml必须在SPARK_CLASSPATH中，简单方法是将该文件放到spark的conf目录下。

# 

```Log
spark-sql>SELECT FROM test.test_hbase_table LIMIT 100;
org.apache.spark.SparkException:Job aborted due to stage failure: Task 0 in stage 0.0 failed 4 times, most recent failure: Lost task 0.3 in stage 0.0 (TID 3, kde-test6, executor 1): Java.Lang.IllegalStateException: unread block data
        at java.io.0bjectInputStreamsBlockDataInputStream.setBlockDataMode(ObjectInputStream.java:2783)
        at java.io.ObjectInputStream.readobjecto(objectInputStream.java:1605)
        at java.io.0bjectInputStream.defaultReadFields(ObjectInputstream.java:2287)
        at java.io.ObjectInputStream,readSerialData(ObjectInputStream.java:2211)
        at java.io..readordinaryobject(objectInputStream.java:2069)
        at java.io.ObjectInputStream.readobjecte(objectInputstream.java:1573)
        at java.io.objectInputstream,readobject(objectInputstream.java:431)
        at org.apache.spark.serializer.JavaDeserializationstrean.readobject(JavaSerializer.scala:75)
        at org.apache.spark.serializer.JavaSerializerInstance.deserialize(JavaSerializer.scala:114)
        at org.apache.spark.executor.ExecutorsTaskRunner.run(Executor.scala:376)
        at java.util.concurrent.ThreadPoolExecutor.runworker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutorsWorker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)

Driver stacktrace:
        at org.apache.spark.scheduler.DAGScheduler.orgSapachessparksschedulerSDAGSchedulerssfailJobAndIndependentStages(DAGScheduler.scala:1891)
        at org.apache.spark.scheduler.DAGSchedulerssanonfunSabortStages1.apply(DAGScheduler.scala:1879)
        at org.apache.spark.scheduler.DAGSchedulerssanonfunSabortStages1.apply(DAGScheduler.scala:1878)
        at scala.collection.mutable.ResizableArraysclass.foreach(ResizableArray.scala:59)
        at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:48)
        at org.apache.spark.scheduler.DAGScheduler.abortStage(DAGScheduler.scala:1878)
        at org.apache.spark.scheduler.DAGSchedulerssanonfunshandleTaskSetFaileds1.apply(DAGScheduler.scala:927)
        at org.apache.spark.scheduler.DAGSchedulerssanon funshandleTaskSetFaileds1.apply(DAGScheduler.scala:927)
        at scala.Option.foreach(Option.scala:257)
        at org.apache.spark.scheduler.DAGScheduler.handleTaskSetFailed(DAGScheduler.scala:927)
        at org.apache.spark.scheduler.DAGSchedulerEventProcessLoop.doOnReceive(DAGScheduler.scala:2112)
        at org.apache.spark.scheduler.DAGSchedulerEventProcessLoop.onReceive(DAGScheduler.scala:2061)
        at org.apache.spark.scheduler.DAGSchedulerEventProcessLoop.onReceive(DAGScheduler.scala:2050)
        at org.apache.spark.util.EventLoopssanons1.run(EventLoop.scala:49)
        at org.apache.spark.scheduler.DAGScheduler.runJob(DAGScheduler.scala:738)
        at org.apache.spark.SparkContext,runJob(SparkContext.scala:2061)
```

## 解决方法一

通过--jars参数指定需要的包，如下：

```Shell
spark-sql --jars /usr/hdp/3.0.1.0-187/hive/lib/hive-hbase-handler-3.1.0.3.0.1.0-187.jar,/usr/lib/ams-hbase/lib/hbase-client-2.0.0.3.0.0.0-1634.jar,/usr/lib/ams-hbase/lib/hbase-common-2.0.0.3.0.0.0-1634.jar,/usr/lib/ams-hbase/lib/hbase-server-2.0.0.3.0.0.0-1634.jar,/usr/lib/ams-hbase/lib/hbase-hadoop2-compat-2.0.0.3.0.0.0-1634.jar,/usr/lib/ams-hbase/lib/guava-11.0.2.jar,/usr/lib/ams-hbase/lib/guava-11.0.2.jar,/usr/lib/ams-hbase/lib/hbase-protocol-2.0.0.3.0.0.0-1634.jar,/usr/lib/ams-hbase/lib/htrace-core-3.2.0-incubating.jar,/usr/lib/ams-hbase/lib/protobuf-java-2.5.0.jar,/usr/lib/ams-hbase/lib/metrics-core-3.2.1.jar
```

## 解决方法二

更优的解决方案。在spark-defaults.conf中添加以下变量：

```Conf
spark.driver.extraClassPath    /usr/hdp/3.0.1.0-187/hive/lib/hive-hbase-handler-3.1.0.3.0.1.0-187.jar:/usr/lib/ams-hbase/lib/hbase-client-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/hbase-common-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/hbase-server-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/hbase-hadoop2-compat-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/guava-11.0.2.jar:/usr/lib/ams-hbase/lib/guava-11.0.2.jar:/usr/lib/ams-hbase/lib/hbase-protocol-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/htrace-core-3.2.0-incubating.jar:/usr/lib/ams-hbase/lib/protobuf-java-2.5.0.jar:/usr/lib/ams-hbase/lib/metrics-core-3.2.1.jar
spark.executor.extraClassPath    /usr/hdp/3.0.1.0-187/hive/lib/hive-hbase-handler-3.1.0.3.0.1.0-187.jar:/usr/lib/ams-hbase/lib/hbase-client-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/hbase-common-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/hbase-server-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/hbase-hadoop2-compat-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/guava-11.0.2.jar:/usr/lib/ams-hbase/lib/guava-11.0.2.jar:/usr/lib/ams-hbase/lib/hbase-protocol-2.0.0.3.0.0.0-1634.jar:/usr/lib/ams-hbase/lib/htrace-core-3.2.0-incubating.jar:/usr/lib/ams-hbase/lib/protobuf-java-2.5.0.jar:/usr/lib/ams-hbase/lib/metrics-core-3.2.1.jar
```