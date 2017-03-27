title: 基于QJM（Quorum Journal Manager）实现 HDFS HA
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-03-27 15:42:38
---

### 综述

#### 集群搭建

Hadoop 集群搭建及服务器信息见我的另外一篇博客[hadoop-2-7-3-集群安装](http://zhang-jc.github.io/2017/03/03/hadoop-2-7-3-%E9%9B%86%E7%BE%A4%E5%AE%89%E8%A3%85/)

#### 服务器列表

- 10.142.166.81：NameNode1/ JournalNode1
- 10.142.166.119：NameNode2 / ResourceManager / JournalNode2 / MapReduce JobHistory Server
- 10.142.165.40：DataNode / NodeManager / JournalNode3
- 10.142.165.41 / 10.142.165.44：DataNode / NodeManager

<!-- more -->

### 架构

![Quorum Journal Manager](/uploads/20170327/hdfs-ha-qjm.png)

为了防止出现“脑裂”情况， JournalNodes 同时只允许一个 NameNode 写入数据。实现 HA 后， Secondary NameNode、CheckpointNode、BackupNode 就都不需要了。

### 配置过程

#### hdfs-site.xml 配置

##### dfs.nameservices

    <property>
      <name>dfs.nameservices</name>
      <value>frin-cluster</value>
    <property>

##### dfs.ha.namenodes.[nameservice id]

    <property>
      <name>dfs.ha.namenodes.frin-cluster</name>
      <value>frin-namenode1,frin-namenode2</value>
    </property>

目前，一个 nameservice 最多只能配置两台 NameNode。

##### dfs.namenode.rpc-address.[nameservice id].[namenode id]

    <property>
      <name>dfs.namenode.rpc-address.frin-cluster.frin-namenode1</name>
      <value>frin-namenode1:8020</value>
    </property>
    <property>
      <name>dfs.namenode.rpc-address.frin-cluster.frin-namenode2</name>
      <value>frin-namenode2:8020</value>
    </property>

##### dfs.namenode.http-address.[nameservice id].[namenode id]

    <property>
      <name>dfs.namenode.http-address.frin-cluster.frin-namenode1</name>
      <value>frin-namenode1:50070</value>
    </property>
    <property>
      <name>dfs.namenode.http-address.frin-cluster.frin-namenode2</name>
      <value>frin-namenode2:50070</value>
    </property>

##### dfs.namenode.shared.edits.dir

    <property>
      <name>dfs.namenode.shared.edits.dir</name>
      <value>qjournal://frin-journalnode1:8485;frin-journalnode2:8485;frin-journalnode3:8485/frin-cluster</value>
    </property>

##### dfs.client.failover.proxy.provider.[nameservice id]

    <property>
      <name>dfs.client.failover.proxy.provider.frin-cluster</name>
      <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
    </property>

##### dfs.ha.fencing.methods

    <property>
      <name>dfs.ha.fencing.methods</name>
      <value>sshfence</value>
    </property>
    <property>
      <name>dfs.ha.fencing.ssh.private-key-files</name>
      <value>/home/hadoop/.ssh/id_rsa</value>
    <property>
    <property>
      <name>dfs.ha.fencing.ssh.connect-timeout</name>
      <value>30000</value>
    </property>

##### dfs.journalnode.edits.dir

    <property>
      <name>dfs.journalnode.edits.dir</name>
      <value>/data/dfs/journal/edits</value>
    </property>

#### core-site.xml fs.defaultFS 配置

    <property>
      <name>fs.defaultFS</name>
      <value>hdfs://frin-namenode1:9000</value>
    </property>

改为

    <property>
      <name>fs.defaultFS</name>
      <value>hdfs://frin-cluster</value>
   </property>

#### 创建目录：

在journalnode节点上建立目录：/data/dfs/journal/edits

#### hosts 配置

/etc/hosts 中添加如下内容：

    10.142.166.81 frin-namenode1
    10.142.166.119 frin-namenode2

    10.142.166.81 frin-journalnode1
    10.142.166.119 frin-journalnode2
    10.142.165.40 frin-journalnode3

    10.142.166.119 frin-resourcemanager1
    10.142.166.119 frin-jobhistoryserver

    10.142.166.81 vm-10-142-166-81
    10.142.166.119 vm-10-142-166-119
    10.142.165.40 vm-10-142-165-40
    10.142.165.41 vm-10-142-165-41
    10.142.165.44 vm-10-142-165-44

### 启动 HDFS HA

#### 停止 NameNode

    hadoop-daemon.sh stop namenode

#### 分发配置

将以下修改后的配置文件分发到集群所有节点：

    core-site.xml
    hdfs-site.xml

#### 重启DataNodes

    hadoop-daemon.sh stop datanode
    hadoop-daemon.sh start datanode

#### 启动 JournalNode

    hadoop-daemon.sh start journalnode

#### 启动 NameNode1

##### 初始化 JournalNode

从非 HA 改为 HA 的 NameNode 需要用 NameNode 本地 edits 目录中的数据初始化 JornalNode：

    hdfs namenode -initializeSharedEdits

##### 启动 NameNode1

    hadoop-daemon.sh start namenode

##### 是 NameNode1 变为 Active

如果 NameNode1 启动后状态为 standby，则用以下命令将 NameNode1 状态变为 active：

    hdfs haadmin -transitionToActive frin-namenode1

#### 启动 NameNode2

##### 初始化 NameNode2

从非 HA 改为 HA 的 NameNode，使用如下命令将 NameNode1 上的元数据信息拷贝到 NameNode2：

    hdfs namenode -bootstrapStandby

##### 启动 NameNode2

    hadoop-daemon.sh start namenode

### 验证

访问 NameNode1 的 Web 站点，可以看到 NameNode1 的状态为 Active：![namenode1.png](/uploads/20170327/namenode1.png)

访问 NameNode2 的 Web 站点，可以看到 NameNode2 的状态为 standby：![namenode2.png](/uploads/20170327/namenode2.png)