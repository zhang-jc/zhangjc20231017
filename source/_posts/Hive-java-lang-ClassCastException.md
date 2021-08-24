---
title: Hive java.lang.ClassCastException
date: 2021-08-24 16:31:21
tags:
- 大数据
- Hive
categories:
- 大数据
- Hive
---

# 部署信息

- Hive 3.1.2
- Hive Metastore Standalone 3.0.0
- Remote Metastore模式

# 错误信息

```Log
2021-08-24T10:24:39,471 ERROR [pool-6-thread-181] metastore.RetryingHMSHandler: java.lang.ClassCastException: org.apache.hadoop.hive.metastore.api.StringColumnStatsData cannot be cast to org.apache.hadoop.hive.metastore.columnstats.cache.StringColumnStatsDataInspector
    at org.apache.hadoop.hive.metastore.columnstats.merge.StringColumnStatsMerger.merge(StringColumnStatsMerger.java:30)
    at org.apache.hadoop.hive.metastore.utils.MetaStoreUtils.mergeColStats(MetaStoreUtils.java:1066)
    at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.set_aggr_stats_for(HiveMetaStore.java:7433)
    at sun.reflect.GeneratedMethodAccessor76.invoke(Unknown Source)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:498)
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:147)
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invoke(RetryingHMSHandler.java:108)
    at com.sun.proxy.$Proxy32.set_aggr_stats_for(Unknown Source)
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17017)
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17001)
    at org.apache.thrift.ProcessFunction.process(ProcessFunction.java:39)
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:111)
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:107)
    at java.security.AccessController.doPrivileged(Native Method)
    at javax.security.auth.Subject.doAs(Subject.java:422)
    at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor.process(TUGIBasedProcessor.java:119)
    at org.apache.thrift.server.TThreadPoolServer$WorkerProcess.run(TThreadPoolServer.java:286)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
    at java.lang.Thread.run(Thread.java:748)

2021-08-24T10:24:39,472 ERROR [pool-6-thread-181] server.TThreadPoolServer: Error occurred during processing of message.
java.lang.ClassCastException: org.apache.hadoop.hive.metastore.api.StringColumnStatsData cannot be cast to org.apache.hadoop.hive.metastore.columnstats.cache.StringColumnStatsDataInspector
    at org.apache.hadoop.hive.metastore.columnstats.merge.StringColumnStatsMerger.merge(StringColumnStatsMerger.java:30) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.utils.MetaStoreUtils.mergeColStats(MetaStoreUtils.java:1066) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.set_aggr_stats_for(HiveMetaStore.java:7433) ~[hive-exec-3.1.2.jar:3.1.2]
    at sun.reflect.GeneratedMethodAccessor76.invoke(Unknown Source) ~[?:?]
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
    at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:147) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invoke(RetryingHMSHandler.java:108) ~[hive-exec-3.1.2.jar:3.1.2]
    at com.sun.proxy.$Proxy32.set_aggr_stats_for(Unknown Source) ~[?:?]
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17017) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17001) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.thrift.ProcessFunction.process(ProcessFunction.java:39) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:111) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:107) ~[hive-exec-3.1.2.jar:3.1.2]
    at java.security.AccessController.doPrivileged(Native Method) ~[?:1.8.0_281]
    at javax.security.auth.Subject.doAs(Subject.java:422) ~[?:1.8.0_281]
    at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762) ~[hadoop-common-3.2.2.jar:?]
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor.process(TUGIBasedProcessor.java:119) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.thrift.server.TThreadPoolServer$WorkerProcess.run(TThreadPoolServer.java:286) [hive-exec-3.1.2.jar:3.1.2]
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_281]
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_281]
    at java.lang.Thread.run(Thread.java:748) [?:1.8.0_281]
2021-08-24T10:24:39,473  INFO [pool-6-thread-181] metastore.HiveMetaStore: 182: Cleaning up thread local RawStore...
2021-08-24T10:24:39,473  INFO [pool-6-thread-181] HiveMetaStore.audit: ugi=hive ip=10.255.0.137 cmd=Cleaning up thread local RawStore...
2021-08-24T10:24:39,474  INFO [pool-6-thread-181] metastore.HiveMetaStore: 182: Done cleaning up thread local RawStore
2021-08-24T10:24:39,474  INFO [pool-6-thread-181] HiveMetaStore.audit: ugi=hive ip=10.255.0.137 cmd=Done cleaning up threadlocal RawStore
2021-08-24T10:24:40,480  INFO [pool-6-thread-181] metastore.HiveMetaStore: 182: Opening raw store with implementation class:org.apache.hadoop.hive.metastore.ObjectStore
2021-08-24T10:24:40,480  INFO [pool-6-thread-181] metastore.ObjectStore: ObjectStore, initialize called
2021-08-24T10:24:40,488  INFO [pool-6-thread-181] metastore.MetaStoreDirectSql: Using direct SQL, underlying DB is MYSQL
2021-08-24T10:24:40,488  INFO [pool-6-thread-181] metastore.ObjectStore: Initialized ObjectStore
2021-08-24T10:24:40,526 ERROR [pool-6-thread-181] metastore.RetryingHMSHandler: java.lang.ClassCastException: org.apache.hadoop.hive.metastore.api.StringColumnStatsData cannot be cast to org.apache.hadoop.hive.metastore.columnstats.cache.StringColumnStatsDataInspector
    at org.apache.hadoop.hive.metastore.columnstats.merge.StringColumnStatsMerger.merge(StringColumnStatsMerger.java:30)
    at org.apache.hadoop.hive.metastore.utils.MetaStoreUtils.mergeColStats(MetaStoreUtils.java:1066)
    at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.set_aggr_stats_for(HiveMetaStore.java:7433)
    at sun.reflect.GeneratedMethodAccessor76.invoke(Unknown Source)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:498)
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:147)
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invoke(RetryingHMSHandler.java:108)
    at com.sun.proxy.$Proxy32.set_aggr_stats_for(Unknown Source)
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17017)
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17001)
    at org.apache.thrift.ProcessFunction.process(ProcessFunction.java:39)
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:111)
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:107)
    at java.security.AccessController.doPrivileged(Native Method)
    at javax.security.auth.Subject.doAs(Subject.java:422)
    at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor.process(TUGIBasedProcessor.java:119)
    at org.apache.thrift.server.TThreadPoolServer$WorkerProcess.run(TThreadPoolServer.java:286)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
    at java.lang.Thread.run(Thread.java:748)

2021-08-24T10:24:40,526 ERROR [pool-6-thread-181] server.TThreadPoolServer: Error occurred during processing of message.
java.lang.ClassCastException: org.apache.hadoop.hive.metastore.api.StringColumnStatsData cannot be cast to org.apache.hadoop.hive.metastore.columnstats.cache.StringColumnStatsDataInspector
    at org.apache.hadoop.hive.metastore.columnstats.merge.StringColumnStatsMerger.merge(StringColumnStatsMerger.java:30) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.utils.MetaStoreUtils.mergeColStats(MetaStoreUtils.java:1066) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.set_aggr_stats_for(HiveMetaStore.java:7433) ~[hive-exec-3.1.2.jar:3.1.2]
    at sun.reflect.GeneratedMethodAccessor76.invoke(Unknown Source) ~[?:?]
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
    at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:147) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invoke(RetryingHMSHandler.java:108) ~[hive-exec-3.1.2.jar:3.1.2]
    at com.sun.proxy.$Proxy32.set_aggr_stats_for(Unknown Source) ~[?:?]
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17017) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$set_aggr_stats_for.getResult(ThriftHiveMetastore.java:17001) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.thrift.ProcessFunction.process(ProcessFunction.java:39) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:111) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:107) ~[hive-exec-3.1.2.jar:3.1.2]
    at java.security.AccessController.doPrivileged(Native Method) ~[?:1.8.0_281]
    at javax.security.auth.Subject.doAs(Subject.java:422) ~[?:1.8.0_281]
    at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762) ~[hadoop-common-3.2.2.jar:?]
    at org.apache.hadoop.hive.metastore.TUGIBasedProcessor.process(TUGIBasedProcessor.java:119) ~[hive-exec-3.1.2.jar:3.1.2]
    at org.apache.thrift.server.TThreadPoolServer$WorkerProcess.run(TThreadPoolServer.java:286) [hive-exec-3.1.2.jar:3.1.2]
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_281]
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_281]
    at java.lang.Thread.run(Thread.java:748) [?:1.8.0_281]
2021-08-24T10:24:40,528  INFO [pool-6-thread-181] metastore.HiveMetaStore: 182: Cleaning up thread local RawStore...
2021-08-24T10:24:40,528  INFO [pool-6-thread-181] HiveMetaStore.audit: ugi=hive ip=10.255.0.135 cmd=Cleaning up thread local RawStore...
2021-08-24T10:24:40,529  INFO [pool-6-thread-181] metastore.HiveMetaStore: 182: Done cleaning up thread local RawStore
2021-08-24T10:24:40,529  INFO [pool-6-thread-181] HiveMetaStore.audit: ugi=hive ip=10.255.0.135 cmd=Done cleaning up threadlocal RawStore
```

# 解决方法
## 方法一：未成功

按照Jira的Bug信息应用patch未能解决。参考链接：[https://issues.apache.org/jira/browse/HIVE-19316](https://issues.apache.org/jira/browse/HIVE-19316)。

## 方法二：成功

临时性解决方案：在hive-site.xml配置中添加以下设置。

```XML
  <property>
    <name>hive.stats.autogather</name>
    <value>false</value>
  </property>
```