title: 记录一次 Sqoop 从 MySQL 导入数据到 Hive 问题的排查经过
date: 2018-12-11 19:07:03
tags:
- Sqoop
- MySQL
- Hive
- 大数据
categories:
- 大数据
- Sqoop
---

#### 问题描述

MySQL 中原始数据有 790W+ 的记录数，在 Sqoop 抽取作业成功的情况下在 Hive 中只有 500W 左右的记录数。

<!-- more -->

#### 排查过程

##### 数据导入脚本 Log

通过 Log 可以发现以下信息：

1. 该 Sqoop 任务被分解为 4 个 MapTask。
2. MapTask 执行期间有异常，是网络异常导致 MySQL 连接不成功。
3. Sqoop 任务对应的 MR 执行过程中总的被调起 9 个 MapTask，其中 3 个失败、2 个被 kill，理论上剩余的 4 个 MapTask 是成功执行的。
4. Sqoop 导入对应的 MR 只有 MapTask，且 MapTask 的数据记录数为 790W+。所以，单纯看 MR 的输出是正常的。
5. Sqoop 导入完成后，紧跟着有一个读取 Sqoop 目标表数据的 insert overwrite 的操作。该操作只被分解为 2 个 MapTask，说明原数据文件只有两个块。
6. 根据以上信息说明 Sqoop 之后确实只生成了 2 个数据文件，有两个文件丢失了。

详细原始 Log 信息见附件：[Sqoop 执行日志](/uploads/20181214/sqoop.txt)

##### 查看 Sqoop 任务对应 MR 的执行日志

根据上面的 Log 中的信息，从 HDFS 上查找对应的日志。Yarn 所有的应用执行日志在 HDFS 的 /data/hadoop/yarn-logs/hadoop/logs/ 目录下。从该目录下查找应用程序 application_1533196506314_4460157 的日志。日志会包含 MR 在各个节点上执行的信息。

从 Log 中发现以下异常信息：

    2018-12-10 00:42:30,595 FATAL [IPC Server handler 17 on 8046] org.apache.hadoop.mapred.TaskAttemptListenerImpl: Task: attempt_1533196506314_4460157_m_000001_0 - exited : org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.LeaseExpiredException): No lease on /user/hive/warehouse/ods.db/bss_customer_fj/_SCRATCH0.3130352759450352/dt=20181209/_temporary/1/_temporary/attempt_1533196506314_4460157_m_000001_0/part-m-00001 (inode 761544109): File does not exist. Holder DFSClient_attempt_1533196506314_4460157_m_000001_0_-1729942809_1 does not have any open files.

所以，怀疑是 MR 在执行结束时，将临时文件移动到正式目录时发生错误。

原始 Log 文件见目录：

[192-168-72-12_27463](/uploads/20181214/application_1533196506314_4460157/192-168-72-12_27463.txt)  
[192-168-72-24_16310](/uploads/20181214/application_1533196506314_4460157/192-168-72-24_16310.txt)  
[192-168-72-84_13498](/uploads/20181214/application_1533196506314_4460157/192-168-72-84_13498.txt)  
[192-168-72-93_53778](/uploads/20181214/application_1533196506314_4460157/192-168-72-93_53778.txt)
[192-168-72-23_18284](/uploads/20181214/application_1533196506314_4460157/192-168-72-23_18284.txt)  
[192-168-72-73_2363](/uploads/20181214/application_1533196506314_4460157/192-168-72-73_2363.txt)  
[192-168-72-88_24481](/uploads/20181214/application_1533196506314_4460157/192-168-72-88_24481.txt)  
[192-168-72-94_54353](/uploads/20181214/application_1533196506314_4460157/192-168-72-94_54353.txt)

##### 查看 DN Log 信息

根据上面的 Log 信息，发现 DN 节点 192-168-72-24 上的 MapTask 各有以下异常信息。

192-168-72-24 异常信息：

    2018-12-10 00:42:34,410 WARN [main] org.apache.hadoop.mapred.YarnChild: Exception running child : org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.LeaseExpiredException): No lease on /user/hive/warehouse/ods.db/bss_customer_fj/_SCRATCH0.3130352759450352/dt=20181209/_temporary/1/_temporary/attempt_1533196506314_4460157_m_000000_0/part-m-00000 (inode 761544157): File does not exist. Holder DFSClient_attempt_1533196506314_4460157_m_000000_0_798513081_1 does not have any open files.

所以怀疑是 MapTask 的最后阶段写文件的时候未成功。检查该 DN 节点的 Log 发现以下异常信息：

    2018-12-10 00:42:32,643 ERROR org.apache.hadoop.hdfs.server.datanode.DataNode: BlockSender.sendChunks() exception:
    java.io.IOException: 断开的管道
            at sun.nio.ch.FileChannelImpl.transferTo0(Native Method)
            at sun.nio.ch.FileChannelImpl.transferToDirectlyInternal(FileChannelImpl.java:428)
            at sun.nio.ch.FileChannelImpl.transferToDirectly(FileChannelImpl.java:493)
            at sun.nio.ch.FileChannelImpl.transferTo(FileChannelImpl.java:608)
            at org.apache.hadoop.net.SocketOutputStream.transferToFully(SocketOutputStream.java:223)
            at org.apache.hadoop.hdfs.server.datanode.BlockSender.sendPacket(BlockSender.java:583)
            at org.apache.hadoop.hdfs.server.datanode.BlockSender.doSendBlock(BlockSender.java:763)
            at org.apache.hadoop.hdfs.server.datanode.BlockSender.sendBlock(BlockSender.java:710)
            at org.apache.hadoop.hdfs.server.datanode.DataXceiver.readBlock(DataXceiver.java:552)
            at org.apache.hadoop.hdfs.protocol.datatransfer.Receiver.opReadBlock(Receiver.java:116)
            at org.apache.hadoop.hdfs.protocol.datatransfer.Receiver.processOp(Receiver.java:71)
            at org.apache.hadoop.hdfs.server.datanode.DataXceiver.run(DataXceiver.java:253)
            at java.lang.Thread.run(Thread.java:748)

百度之后发现该异常信息跟 DN 的一个配置有关系，具体配置项是 DN 可以同时处理的文件上限。对于老版本配置项名称为“dfs.datanode.max.xcievers”，对于新版本配置项名称改为“dfs.datanode.max.transfer.threads”。该参数的默认值为 4096，所以需要修改为 8192。

##### 查看 NN Log 信息

默认的，Log4j 输出的 NameNode 日志文件只保留最近的 20 个文件。因为 NN 的 Log 信息比较多，20 个文件保存的日志不足 1 天，异常时间的日志已经被冲掉了。

![NameNode Log](/uploads/20181214/namenodeLogs.png)

##### 查看 NN 信息

通过 NN WebUI 查看发现 NN 内存的使用已经 70% 左右，负载已经比较高。

![NameNode Info](/uploads/20181214/namenodeInfo.png)

##### 处理措施

综合以上信息，其实问题发生的根本原因未能查明。基于对 Hadoop 了解的深度、精力及对故障恢复的容忍程度的考虑，待定位根本原因再解决问题的方案不可控。所以，采取以下改进措施：

（1）修改 DN 最大处理文件数量上限至 8192。
（2）将 NN 内存扩展到 100G。
（3）修改 Sqoop 源代码，当落地到目标 HDFS 目录下的文件数量与 MapTask 数量不一致时返回错误状态，并由调度系统进行重新抽取。
