---
title: HiveServer2未启用服务自动发现但报连接Zookeeper超时错误
date: 2021-08-11 15:33:27
tags:
- Hive
- Zookeeper
- 大数据
categories:
- 大数据
- Hive
---

```Log
2021-08-11T15:03:39,151 ERROR [main] curator.ConnectionState: Connection timed out for connection string (:2181) and timeout (15000) / elapsed (15058)
org.apache.curator.CuratorConnectionLossException: KeeperErrorCode = ConnectionLoss
        at org.apache.curator.ConnectionState.checkTimeouts(ConnectionState.java:225) ~[curator-client-2.12.0.jar:?]
        at org.apache.curator.ConnectionState.getZooKeeper(ConnectionState.java:94) ~[curator-client-2.12.0.jar:?]
        at org.apache.curator.CuratorZookeeperClient.getZooKeeper(CuratorZookeeperClient.java:117) ~[curator-client-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CuratorFrameworkImpl.getZooKeeper(CuratorFrameworkImpl.java:489) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl$11.call(CreateBuilderImpl.java:740) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl$11.call(CreateBuilderImpl.java:723) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.RetryLoop.callWithRetry(RetryLoop.java:109) ~[curator-client-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl.pathInForeground(CreateBuilderImpl.java:720) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl.protectedPathInForeground(CreateBuilderImpl.java:484) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl.forPath(CreateBuilderImpl.java:474) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl.forPath(CreateBuilderImpl.java:454) ~[curator-framework-2.12.0.jar:?]
        at org.apache.curator.framework.imps.CreateBuilderImpl.forPath(CreateBuilderImpl.java:44) ~[curator-framework-2.12.0.jar:?]
        at org.apache.hive.service.server.HiveServer2.startZookeeperClient(HiveServer2.java:490) ~[hive-service-3.1.2.jar:3.1.2]
        at org.apache.hive.service.server.HiveServer2.startPrivilegeSynchonizer(HiveServer2.java:1002) ~[hive-service-3.1.2.jar:3.1.2]
        at org.apache.hive.service.server.HiveServer2.start(HiveServer2.java:726) ~[hive-service-3.1.2.jar:3.1.2]
        at org.apache.hive.service.server.HiveServer2.startHiveServer2(HiveServer2.java:1037) ~[hive-service-3.1.2.jar:3.1.2]
        at org.apache.hive.service.server.HiveServer2.access$1600(HiveServer2.java:140) ~[hive-service-3.1.2.jar:3.1.2]
        at org.apache.hive.service.server.HiveServer2$StartOptionExecutor.execute(HiveServer2.java:1305) ~[hive-service-3.1.2.jar:3.1.2]
        at org.apache.hive.service.server.HiveServer2.main(HiveServer2.java:1149) ~[hive-service-3.1.2.jar:3.1.2]
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:1.8.0_281]
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:1.8.0_281]
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_281]
        at java.lang.reflect.Method.invoke(Method.java:498) ~[?:1.8.0_281]
        at org.apache.hadoop.util.RunJar.run(RunJar.java:323) ~[hadoop-common-3.2.2.jar:?]
        at org.apache.hadoop.util.RunJar.main(RunJar.java:236) ~[hadoop-common-3.2.2.jar:?]
2021-08-11T15:03:39,535  INFO [main-SendThread(localhost:2181)] zookeeper.ClientCnxn: Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2021-08-11T15:03:39,535  WARN [main-SendThread(localhost:2181)] zookeeper.ClientCnxn: Session 0x0 for server null, unexpected error, closing socket connection and attempting reconnect
```

查看hive的配置发现以下配置项：

```XML
  <property>
    <name>hive.zookeeper.quorum</name>
    <value/>
    <description>
      List of ZooKeeper servers to talk to. This is needed for: 
      1. Read/write locks - when hive.lock.manager is set to 
      org.apache.hadoop.hive.ql.lockmgr.zookeeper.ZooKeeperHiveLockManager, 
      2. When HiveServer2 supports service discovery via Zookeeper.
      3. For delegation token storage if zookeeper store is used, if
      hive.cluster.delegation.token.store.zookeeper.connectString is not set
      4. LLAP daemon registry service
      5. Leader selection for privilege synchronizer
    </description>
  </property>
```

此处报错就是因为hiveserver2在启动privilege synchronizer时连接不上zk导致的。但是这个配置项的作用到底是什么呢？这个问题需要继续探索。