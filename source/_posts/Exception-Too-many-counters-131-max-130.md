---
title: 'Exception: Too many counters: 131 max=130'
date: 2021-08-25 14:56:30
tags:
- 大数据
- Hive
- Tez
categories:
- 大数据
- Hive
---

完整异常信息如下：

```Log
Exception: Too many counters: 131 max=130
	at org.apache.tez.common.counters.Limits.checkCounters(Limits.java:86)
	at org.apache.tez.common.counters.Limits.incrCounters(Limits.java:93)
	at org.apache.tez.common.counters.AbstractCounterGroup.addCounter(AbstractCounterGroup.java:76)
	at org.apache.tez.common.counters.AbstractCounterGroup.addCounterImpl(AbstractCounterGroup.java:93)
	at org.apache.tez.common.counters.AbstractCounterGroup.findCounter(AbstractCounterGroup.java:104)
	at org.apache.tez.common.counters.AbstractCounterGroup.aggrAllCounters(AbstractCounterGroup.java:204)
	at org.apache.tez.common.counters.AbstractCounters.aggrAllCounters(AbstractCounters.java:372)
	at org.apache.tez.dag.app.dag.impl.DAGImpl.aggrTaskCounters(DAGImpl.java:776)
	at org.apache.tez.dag.app.dag.impl.DAGImpl.getAllCounters(DAGImpl.java:725)
	at org.apache.tez.dag.app.dag.impl.DAGImpl.getDAGStatus(DAGImpl.java:941)
	at org.apache.tez.dag.app.dag.impl.DAGImpl.getDAGStatus(DAGImpl.java:984)
	at org.apache.tez.dag.api.client.DAGClientHandler.getDAGStatus(DAGClientHandler.java:73)
	at org.apache.tez.dag.api.client.rpc.DAGClientAMProtocolBlockingPBServerImpl.getDAGStatus(DAGClientAMProtocolBlockingPBServerImpl.java:105)
	at org.apache.tez.dag.api.client.rpc.DAGClientAMProtocolRPC$DAGClientAMProtocol$2.callBlockingMethod(DAGClientAMProtocolRPC.java:7642)
	at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:528)
	at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:1086)
	at org.apache.hadoop.ipc.Server$RpcCall.run(Server.java:1029)
	at org.apache.hadoop.ipc.Server$RpcCall.run(Server.java:957)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
	at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2957)
```

解决方法是在hive-site.xml中添加如下配置：

```XML
  <property>
    <name>hive.stats.autogather</name>
    <value>false</value>
  </property>
```
