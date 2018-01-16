title: HDFS Rebalance
tags:
- Hadoop
- HDFS
categories:
- 大数据
- Hadoop
---

### 相关配置项

1. dfs.datanode.balance.bandwidthPerSec

DataNode 用于数据 balance 的最大带宽，单位 byte / s。

默认值：1048576（1M）

2. dfs.datanode.fsdataset.volume.choosing.policy

从目录列表选择卷的策略的类名。默认是 org.apache.hadoop.hdfs.server.datanode.fsdataset.RoundRobinVolumeChoosingPolicy。如果考虑可用的磁盘空间，设置为“org.apache.hadoop.hdfs.server.datanode.fsdataset.AvailableSpaceVolumeChoosingPolicy”。

> 2.7.0 版本文档中没有这个参数的解释，2.9.0 版本文档中有描述。Jira 链接：<https://issues.apache.org/jira/browse/HDFS-8356?jql=text%20~%20%22dfs.datanode.fsdataset.volume.choosing.policy%22>

3. dfs.datanode.available-space-volume-choosing-policy.balanced-space-threshold

只有当 dfs.datanode.fsdataset.volume.choosing.policy 设置为 org.apache.hadoop.hdfs.server.datanode.fsdataset.AvailableSpaceVolumeChoosingPolicy 时该参数才会被使用。这个设置控制 DN 卷之间的可用磁盘空间差异多少字节内认为它们是均衡的。如果所有卷可用空间的差异都在这个范围内，那么所有卷被认为是均衡的，并且块会以纯轮询的方式进行分配。

默认值：10737418240（10G）

4. dfs.datanode.available-space-volume-choosing-policy.balanced-space-preference-fraction

只有当 dfs.datanode.fsdataset.volume.choosing.policy 设置为 org.apache.hadoop.hdfs.server.datanode.fsdataset.AvailableSpaceVolumeChoosingPolicy 时该参数才会被使用。这个设置控制新块分配给有更多可用空间的卷的比率。这个设置应该在 0.0 - 1.0 之间，但是实践中应该设置为 0.5 - 1.0，因为没有原因应该让可用空间少的卷收到更多的块。 

默认值：0.75f

5. dfs.balancer.keytab.enabled

设置为 true 为基于 Kerberos 认证的 Hadoop 启用使用 keytab 进行登陆。

默认值：false

6. dfs.balancer.address

为基于 Kerberos 登陆的 keytab 设置 hostname。基于 keytab 登陆可以用 dfs.balancer.keytab.enabled 参数启用。

默认值：0.0.0.0:0

7. dfs.balancer.keytab.file

作为 Balancer 服务登陆使用的 keytab 文件。用 dfs.balancer.kerberos.principal 配置服务主体的名称。基于 keytab 登陆可以用 dfs.balancer.keytab.enabled 参数启用。

8. dfs.balancer.kerberos.principal

Balancer proncipal。一般会设置为 balancer/\_HOST@REALM.TLD。Balancer 启动时会用它自己的完全限定主机名替代 _HOST。\_HOST 占位符允许在不同的服务器上使用相同的配置。基于 keytab 登陆可以用 dfs.balancer.keytab.enabled 参数启用。

9. dfs.balancer.block-move.timeout

移动一个块的最大时间毫秒数。如果这个参数设置大于 0，在这个时间后 Balancer 将停止等待一个块完成移动。在典型的集群中，3 到 5 分钟超时是合理的。如果块移动发生超时的概率比较大，这个参数需要提高。这可能是分派了太多的工作并且很多节点同时超过带宽限制的结果。这种情况，其他 Balancer 参数可能需要调整。默认该功能被禁用，参数值为 0。 

默认值：0

10. dfs.balancer.max-no-move-interval

如果过了指定的时间，并且没有块从源 DataNode 移出，在当前 Balancer 循环中不会从这个 DataNode 移出块。

默认值：60000

### Balancer

#### 用法

    hdfs balancer
          [-threshold <threshold>]
          [-policy <policy>]
          [-exclude [-f <hosts-file> | <comma-separated list of hosts>]]
          [-include [-f <hosts-file> | <comma-separated list of hosts>]]
          [-idleiterations <idleiterations>]

#### 参数说明

1. -policy <policy>：
    datanode（默认）：如果每个 DataNode 是均衡的那么集群是均衡的。  
    blockpool：如果每个 DataNode 上的 block pool 是均衡的那么集群是均衡的。
2. -threshold <threshold>：
    磁盘容量百分比。这个参数覆盖默认的阀值。
3. -exclude -f <hosts-file> | <comma-separated list of hosts>：
    从被 Balancer 均衡的节点中排除指定的 DataNode。
4. -include -f <hosts-file> | <comma-separated list of hosts>：
    Balancer 只均衡指定的 DataNode。
5. -idleiterations <iterations>：
    退出的最大空闲循环数。这个参数覆盖默认循环数（5）。

#### 说明

运行一个 Balancer 功能，管理员可以简单的按 Ctrl + C 停止均衡过程。参考 [Balancer](http://hadoop.apache.org/docs/r2.7.4/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#Balancer) 获取更多详细说明。

注意，blockpool 策略比 datanode 策略更严格。

