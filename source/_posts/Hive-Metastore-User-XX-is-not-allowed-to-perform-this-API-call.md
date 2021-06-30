title: 'Hive Metastore: User XX is not allowed to perform this API call'
date: 2021-06-30 16:21:53
tags:
- 大数据
- Hive
categories:
- 大数据
- Hive
---

HiveServer2启动错误信息如下：

    ```Text
    2021-06-30T11:47:08,197  WARN [main] server.HiveServer2: Error starting HiveServer2 on attempt 22, will retry in 60000ms
    java.lang.RuntimeException: Error initializing notification event poll
      at org.apache.hive.service.server.HiveServer2.init(HiveServer2.java:275) ~[hive-service-3.1.2.jar:3.1.2]
      at org.apache.hive.service.server.HiveServer2.startHiveServer2(HiveServer2.java:1036) [hive-service-3.1.2.jar:3.1.2]
      at org.apache.hive.service.server.HiveServer2.access$1600(HiveServer2.java:140) [hive-service-3.1.2.jar:3.1.2]
      at org.apache.hive.service.server.HiveServer2$StartOptionExecutor.execute(HiveServer2.java:1305) [hive-service-3.1.2.jar:3.1.2]
      at org.apache.hive.service.server.HiveServer2.main(HiveServer2.java:1149) [hive-service-3.1.2.jar:3.1.2]
      at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:1.8.0_281]
      at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:1.8.0_281]
      at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
      at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
      at org.apache.hadoop.util.RunJar.run(RunJar.java:323) [hadoop-common-3.2.2.jar:?]
      at org.apache.hadoop.util.RunJar.main(RunJar.java:236) [hadoop-common-3.2.2.jar:?]
    Caused by: java.io.IOException: org.apache.thrift.TApplicationException: Internal error processing get_current_notificationEventId
      at org.apache.hadoop.hive.metastore.messaging.EventUtils$MSClientNotificationFetcher.getCurrentNotificationEventId(EventUtils.java:75) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.ql.metadata.events.NotificationEventPoll.<init>(NotificationEventPoll.java:103) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.ql.metadata.events.NotificationEventPoll.initialize(NotificationEventPoll.java:59) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hive.service.server.HiveServer2.init(HiveServer2.java:273) ~[hive-service-3.1.2.jar:3.1.2]
      ... 10 more
    Caused by: org.apache.thrift.TApplicationException: Internal error processing get_current_notificationEventId
      at org.apache.thrift.TApplicationException.read(TApplicationException.java:111) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.thrift.TServiceClient.receiveBase(TServiceClient.java:79) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Client.recv_get_current_notificationEventId(ThriftHiveMetastore.java:5575) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Client.get_current_notificationEventId(ThriftHiveMetastore.java:5563) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.getCurrentNotificationEventId(HiveMetaStoreClient.java:2723) ~[hive-exec-3.1.2.jar:3.1.2]
      at sun.reflect.GeneratedMethodAccessor5.invoke(Unknown Source) ~[?:?]
      at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
      at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
      at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.invoke(RetryingMetaStoreClient.java:212) ~[hive-exec-3.1.2.jar:3.1.2]
      at com.sun.proxy.$Proxy27.getCurrentNotificationEventId(Unknown Source) ~[?:?]
      at sun.reflect.GeneratedMethodAccessor5.invoke(Unknown Source) ~[?:?]
      at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
      at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
      at org.apache.hadoop.hive.metastore.HiveMetaStoreClient$SynchronizedHandler.invoke(HiveMetaStoreClient.java:2773) ~[hive-exec-3.1.2.jar:3.1.2]
      at com.sun.proxy.$Proxy27.getCurrentNotificationEventId(Unknown Source) ~[?:?]
      at org.apache.hadoop.hive.metastore.messaging.EventUtils$MSClientNotificationFetcher.getCurrentNotificationEventId(EventUtils.java:73) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.ql.metadata.events.NotificationEventPoll.<init>(NotificationEventPoll.java:103) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hadoop.hive.ql.metadata.events.NotificationEventPoll.initialize(NotificationEventPoll.java:59) ~[hive-exec-3.1.2.jar:3.1.2]
      at org.apache.hive.service.server.HiveServer2.init(HiveServer2.java:273) ~[hive-service-3.1.2.jar:3.1.2]
      ... 10 more
    ```
    
查看Metastore日志错误信息如下：

```Text
2021-06-30T11:44:45,568 ERROR [pool-6-thread-77] thrift.ProcessFunction: Internal error processing get_current_notificationEventId
org.apache.thrift.TException: MetaException(message:User root is not allowed to perform this API call)
  at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.get_current_notificationEventId(HiveMetaStore.java:7481) ~[hive-exec-3.1.2.jar:3.1.2]
  at sun.reflect.GeneratedMethodAccessor6.invoke(Unknown Source) ~[?:?]
  at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
  at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
  at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invokeInternal(RetryingHMSHandler.java:147) ~[hive-exec-3.1.2.jar:3.1.2]
  at org.apache.hadoop.hive.metastore.RetryingHMSHandler.invoke(RetryingHMSHandler.java:108) ~[hive-exec-3.1.2.jar:3.1.2]
  at com.sun.proxy.$Proxy26.get_current_notificationEventId(Unknown Source) ~[?:?]
  at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$get_current_notificationEventId.getResult(ThriftHiveMetastore.java:18364) ~[hive-exec-3.1.2.jar:3.1.2]
  at org.apache.hadoop.hive.metastore.api.ThriftHiveMetastore$Processor$get_current_notificationEventId.getResult(ThriftHiveMetastore.java:18349) ~[hive-exec-3.1.2.jar:3.1.2]
  at org.apache.thrift.ProcessFunction.process(ProcessFunction.java:39) [hive-exec-3.1.2.jar:3.1.2]
  at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:111) [hive-exec-3.1.2.jar:3.1.2]
  at org.apache.hadoop.hive.metastore.TUGIBasedProcessor$1.run(TUGIBasedProcessor.java:107) [hive-exec-3.1.2.jar:3.1.2]
  at java.security.AccessController.doPrivileged(Native Method) [?:1.8.0_281]
  at javax.security.auth.Subject.doAs(Subject.java:422) [?:1.8.0_281]
  at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762) [hadoop-common-3.2.2.jar:?]
  at org.apache.hadoop.hive.metastore.TUGIBasedProcessor.process(TUGIBasedProcessor.java:119) [hive-exec-3.1.2.jar:3.1.2]
  at org.apache.thrift.server.TThreadPoolServer$WorkerProcess.run(TThreadPoolServer.java:286) [hive-exec-3.1.2.jar:3.1.2]
  at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) [?:1.8.0_281]
  at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) [?:1.8.0_281]
  at java.lang.Thread.run(Thread.java:748) [?:1.8.0_281]
Caused by: org.apache.hadoop.hive.metastore.api.MetaException: User root is not allowed to perform this API call
  at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.authorizeProxyPrivilege(HiveMetaStore.java:7517) ~[hive-exec-3.1.2.jar:3.1.2]
  at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.get_current_notificationEventId(HiveMetaStore.java:7477) ~[hive-exec-3.1.2.jar:3.1.2]
  ... 19 more
```

解决方法如下：

- 改用hive用户启动hiveserver2和metastore。
- 在hadoop配置文件core-site.xml中增加以下配置，并更新集群及客户端。重启metastore及hiveserver2。

```XML
<property>
  <name>hadoop.proxyuser.hive.groups</name>
  <value>*</value>
</property>
<property>
  <name>hadoop.proxyuser.hive.hosts</name>
  <value>*</value>
</property>
```