title: 浅谈 Hadoop NameNode、SecondaryNameNode、CheckPoint Node、BackupNode
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-01-08 21:40:34
---

### NameNode

Hadoop NameNode 管理着文件系统的 Namespace，它维护着整个文件系统树（FileSystem Tree）以及文件树中所有的文件和文件夹元数据（Metadata）。

NameNode Metadata 主要是两个文件：edits 和 fsimage。fsimage 是 HDFS 的最新状态（截止到 fsimage 文件创建时间的最新状态）文件，而 edits 是自 fsimage 创建后的 Namespace 操作日志。NameNode 每次启动的时候，都要合并两个文件，按照 edits 的记录，把 fsimage 文件更新到最新。

<!-- more -->

### Secondary NameNode

Hadoop SecondaryNameNode 并不是 Hadoop 第二个 NameNode，它不提供 NameNode 服务，而仅仅是 NameNode 的一个工具，帮助 NameNode 管理 Metadata 数据。

一般情况下，当  NameNode 重启的时候，会合并硬盘上的 fsimage 文件和 edits 文件，得到完整的 Metadata 信息。但是，如果集群规模十分庞大，操作频繁，那么 edits 文件就会非常大，这个合并过程就会非常慢，导致 HDFS 长时间无法启动。如果定时将 edits 文件合并到 fsimage，那么重启 NameNode 就可以非常快，而 Secondary NameNode 就做这个合并的工作。

Secondary NameNode 定期地从 NameNode 上获取元数据。当它准备获取元数据的时候，就通知 NameNode 暂停写入 edits 文件。NameNode收到请求后停止写入 edits 文件，之后的 log 记录写入一个名为 edits.new 的文件。Secondary NameNode 获取到元数据以后，把 edits 文件和 fsimage 文件在本机进行合并，创建出一个新的 fsimage 文件，然后把新的 fsimage 文件发送回 NameNode。NameNode 收到 Secondary NameNode 发回的 fsimage 后，就拿它覆盖掉原来的 fsimage 文件，并删除 edits 文件，把 edits.new 重命名为 edits。

通过这样一番操作，就避免了 NameNode 的 edits 日志的无限增长，加速 Namenode 的启动过程。

![Secondary NameNode](/uploads/20170108/secondarynamenode.png)

### CheckPoint Node

可能是由于 Secondary NameNode 容易对人产生误导，因此 Hadoop 1.0.4 之后建议不要使用 Secondary NameNode，而使用 CheckPoint Node。Checkpoint Node 和 Secondary NameNode 的作用以及配置完全相同，只是启动命令不同 bin/hdfs namenode -checkpoint。

### Backup Node

Backup Node 在内存中维护了一份从 NameNode 同步过来的 fsimage，同时它还从 NameNode 接收 edits 文件的日志流，并把它们持久化硬盘，Backup Node 把收到的这些 edits 文件和内存中的 fsimage 文件进行合并，创建一份元数据备份。虽然 BackupNode 是一个备份的 NameNode 节点，不过 Backup Node 目前还无法直接接替 NameNode 提供服务。因此当前版本的 Backup Node 还不具有热备功能，也就是说，当 NameNode 发生故障，目前还只能通过重启 NameNode 的方式来恢复服务。

不过在 Hadoop 2.x 中提出了 Hadoop HA 的一些策略，实现了 Hadoop NameNode 的 failover。