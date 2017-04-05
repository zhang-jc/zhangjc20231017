title: 基于 Zookeeper 的 HDFS QJM（Quorum Journal Manager） HA 自动 Failover
tags:
  - HDFS
  - Zookeeper
categories:
  - 大数据
  - Hadoop
date: 2017-04-05 17:04:26
---

### 组件

#### Zookeeper

HDFS 自动 failover 的实现基于 Zookeeper 的以下能力：

- **失败检查**：每台 NameNode 服务器在 Zookeeper 中维持一个持久会话。如果服务器崩溃，Zookeeper 会话就会超时，并通知另外一台 NameNode 触发 failover。
- **活跃 NameNode 选举**：Zookeeper 提供了一种简单机制唯一选举一台活跃节点。如果当前活跃的 NameNode 崩溃，另外一个台节点可以在 Zookeeper 中获取一个特定且唯一的锁，表明它应该变为下一个状态。

<!-- more -->

#### ZKFailoverController (ZKFC)

ZKFailoverController (ZKFC) 是一个 Zookeeper 的客户端，它负责监测和管理 NameNode 的状态。运行 NameNode 的机器也会运行一个 ZKFC。ZKFC 负责：

- **健康监测**：ZKFC 使用一个健康检查命令周期的 ping 它本地的 NameNode。只要节点响应及时，ZKFC 认为 NameNode 是健康的。如果节点崩溃、卡住或进入不健康状态，健康监测会标识它是不健康的。

- **ZooKeeper 会话管理**：本地 NameNode 健康时，ZKFC 在 Zookeeper 中保持一个打开的会话。如果本地的 NameNode 是活跃的，它也会保持一个特定的“lock”znode。这个锁使用了 Zookeeper 支持的“ephemeral”节点；如果会话过期，这个锁节点被自动删除。

- **基于 Zookeeper 的选举**：如果本地 NameNode 是健康的，并且 ZKFC 发现当前没有其他节点保持锁 znode，它自己将尝试请求这个锁。如果成功，那么它“赢得选举”，并且负责运行 failover 来使它本地的 NameNode 活跃。failover 进程与之前描述的手动 failover 一样：首先，如果需要的话之前活跃的节点会被杀死；然后，本地 NameNode 变为活跃状态。

### 部署 Zookeeper

见我的另外一篇博客[Zookeeper 集群搭建](http://zhang-jc.github.io/2017/04/01/Zookeeper-%E9%9B%86%E7%BE%A4%E6%90%AD%E5%BB%BA/)。

### 停止集群

在开始配置之前先停止集群服务。

### 配置自动 failover

#### hdfs-site.xml

添加如下配置：

    <property>
      <name>dfs.ha.automatic-failover.enabled</name>
      <value>true</value>
    </property>

#### core-site.xml

添加如下配置：

    <property>
      <name>ha.zookeeper.quorum</name>
      <value>frin-zookeeper1:2181,frin-zookeeper2:2181,frin-zookeeper3:2181</value>
    </property>

### 初始化 ZooKeeper 中的 HA 状态

从一台 NameNode 主机上用以下命令初始化：

    bin/hdfs zkfc -formatZK

### 启动集群

启动 NameNode 和 DataNode。

在两台 NameNode 主机上使用下面的命令启动 zkfc 守护进程：

    sbin/hadoop-daemon.sh start zkfc

### 安全访问 ZooKeeper

如果我们运行的是一个安全集群，那我们也希望确保存在 ZooKeeper 中的信息也是安全的。这可以防止恶意的客户端修改 ZooKeeper 中的元数据或触发一个潜在错误的 failover。

#### core-site.xml

为了保护 Zookeeper 中的信息，首先在 core-site.xml 中添加如下配置：

    <property>
      <name>ha.zookeeper.auth</name>
      <value>@/usr/local/hadoop/etc/hadoop/zk-auth.txt</value>
    </property>
    <property>
      <name>ha.zookeeper.acl</name>
      <value>@/usr/local/hadoop/etc/hadoop/zk-acl.txt</value>
    </property>

注意，配置中的“@”字符表示配置不是内部的值，而是指向磁盘上的文件。

第一个文件指定了 Zookeeper 授权列表，与 ZK CLI 中使用的格式一样。例如：

    digest:hdfs-zkfcs:mypassword

hdfs-zkfcs 是 Zookeeper 中一个唯一的用户名，mypassword 是密码。

在 NameNode 节点 $HADOOP_HOME/etc/hadoop/ 目录下创建文件 zk-auth.txt 和 zk-acl.txt。zk-auth.txt 配置文件中的内容为：

    digest:hdfs-zkfcs:frin-cluster-hdfs-zkfcs

#### ZooKeeper ACL

生成一个跟上面授权对应的 ZooKeeper ACL，使用下面的命令：

    java -cp /usr/local/zookeeper/lib/*:/usr/local/zookeeper/zookeeper-3.4.10.jar org.apache.zookeeper.server.auth.DigestAuthenticationProvider hdfs-zkfcs:frin-cluster-hdfs-zkfcs

拷贝输出内容中“->”之后的内容，粘贴到 zk-acls.txt 文件中，并且要以“digest:”作为前缀。例如：

    digest:hdfs-zkfcs:+ub6J925c+Pbmbd74jrgDRKLe5o=:rwcda

#### 初始化 Zookeeper 中的状态

为了这些 ACL 生效，像上面描述的重新执行 zkfc -formatZK 命令。

做完这些后，从 ZK CLI 确认 ACL，如下面：

    [zk: frin-zookeeper1:2181(CONNECTED) 3] getAcl /hadoop-ha
    'digest,'hdfs-zkfcs:+ub6J925c+Pbmbd74jrgDRKLe5o=
    : cdrwa

### 验证自动 failover

从 WebUI 找出活跃的 NameNode，使用 kill -9 &lt;NameNode 进程的 PID&gt; 模拟 JVM 崩溃，也可以重启服务器或者使服务器断网。这些情况发生后，我们希望另外一台 NameNode 能在几秒钟内自动变为活跃状态。监测失败及触发 failover 需要的时间依赖 ha.zookeeper.session-timeout.ms 配置项，默认为 5 秒。

### 自动 Failover FAQ

#### 是否需要按照特定的顺序启动 ZKFC 和 NameNode？

不。在任何一个给定的节点上，可以在相关的 NameNode 进程启动之前或者之后启动 ZKFC。

#### 还应该添加什么样的监测？

应该在运行 NameNode 的主机上添加监测确保 ZKFC 保持运行。例如，在 Zookeeper 失败的一些场景，ZKFC 可能异常退出，应该重启 ZKFC 确保系统准备好 failover。

另外，应该监测 Zookeeper 集群的每台服务器。如果 Zookeeper 崩溃，那么自动 failover 将不起作用。

#### 如果 Zookeeper 停止会发生什么？

如果 ZooKeeper 集群崩溃，自动 failover 将不会被触发。然而，HDFS 会继续运行不受影响。当 Zookeeper 重新启动，HDFS 会重新连接。

#### 是否可以指定主/优先的 NameNode？

不。当前，这是不支持的。哪个 NameNode 先启动哪个节点将变为活跃的。可以选择以特定的顺序启动集群，先启动作为优先节点的 NameNode 节点。

#### 当配置了自动 failover 后，如何发起手动 failover？

即使配置了自动 failover，可以使用同样的 hdfs haadmin 命令发起手动 failover。它将执行相应的 failover。

### 启用 HA 的 HDFS 升级/定型/回退

当切换 HDFS 的版本时，有时新的软件可以简单的安装并重启集群。然而，有时升级正在运行的 HDFS 版本时需要修改磁盘上的数据。这种情况下，必须在安装完新软件后使用 HDFS 的升级/定型/回退工具。在  HA 的情况下这个过程会变得复杂，因为  NN 依赖的磁盘上的元数据是分布定义的，在一对 HA NN 上，在使用 QJM 共享 edits 的情况下也存储在 JournalNode 上。这一节描述在 HA 的情况下使用 HDFS 升级/定型/回退工具的过程。

#### 执行 HA 升级

必须做下面的操作：

1. 正常关闭所有 NN，并安装新软件。
2. 启动所有的 JN。注意，当执行升级、回退或者定型操作时所有的 JN 都是运行的，这是极为重要的。如果在运行这些操作时任何 JN 停止了，那么操作都会失败。
3. 使用 -upgrade 标志启动一台 NN。
4. 开始，在 HA 情况下这台 NN 通常不会进入 standby 状态。而是，这台 NN 会立即进入 active 状态，执行它本地存储目录的更新，并执行共享 edit 日志的更新。
5. 此时，另外一台 NN 与已经升级的 NN 是不同步的。为了同步及恢复 HA 安装，需要使用 -bootstrapStandby 标志重新引导这台 NN 运行。使用 -upgrade 标志启动这台 NN 是错误的。

注意，在定型或者回退升级之前的任何时候想重启 NameNode，应该正常启动 NN，不使用任何特殊的启动标志。

#### 定型 HA 升级

当 NN 都正在运行且一台为 active，操作者应该使用 hdfs dfsadmin -finalizeUpgrade 命令定型 HA 升级。此时，活跃的 NN 将执行共享 edit 日志的定型，并且包含之前 FS 状态的存储目录的这台 NN 会删除它本地的状态。

#### 执行升级回退

停止两台 NN。在发起升级过程的那台 NN 上运行回退命令，它将在本地目录、共享日志、NFS 或 JN 上执行回退。之后，启动这台 NN，并在另外一个 NN 上运行 -bootstrapStandby，以使两台 NN 同步到回退后的文件系统状态。

### 问题排查

#### code:NOAUTH

在测试自动切换时未能成功，检查 zkfc 的 log 发现以下错误信息：

    java.lang.RuntimeException: ZK Failover Controller failed: Received create error from Zookeeper. code:NOAUTH for path /hadoop-ha/frin-cluster/ActiveStandbyElectorLock

可以发现是因为没有授权所以访问 Zookeeper 中的锁信息失败。经排查是配置安全访问 Zookeeper 时修改了 core-site.xml 中的配置，但配置没有分发到其他服务器。

#### .ssh/id_rsa

在测试自动切换时未能成功，检查 zkfc 的 log 发现以下错误信息：

    2017-04-05 15:01:06,635 WARN org.apache.hadoop.ha.SshFenceByTcpPort: Unable to create SSH session
    com.jcraft.jsch.JSchException: java.io.FileNotFoundException: /home/hadoop/.ssh/id_rsa (No such file or directory)
            at com.jcraft.jsch.IdentityFile.newInstance(IdentityFile.java:98)
            at com.jcraft.jsch.JSch.addIdentity(JSch.java:206)
            at com.jcraft.jsch.JSch.addIdentity(JSch.java:192)
            at org.apache.hadoop.ha.SshFenceByTcpPort.createSession(SshFenceByTcpPort.java:122)
            at org.apache.hadoop.ha.SshFenceByTcpPort.tryFence(SshFenceByTcpPort.java:91)
            at org.apache.hadoop.ha.NodeFencer.fence(NodeFencer.java:97)
            at org.apache.hadoop.ha.ZKFailoverController.doFence(ZKFailoverController.java:532)
            at org.apache.hadoop.ha.ZKFailoverController.fenceOldActive(ZKFailoverController.java:505)
            at org.apache.hadoop.ha.ZKFailoverController.access$1100(ZKFailoverController.java:61)
            at org.apache.hadoop.ha.ZKFailoverController$ElectorCallbacks.fenceOldActive(ZKFailoverController.java:892)
            at org.apache.hadoop.ha.ActiveStandbyElector.fenceOldActive(ActiveStandbyElector.java:910)
            at org.apache.hadoop.ha.ActiveStandbyElector.becomeActive(ActiveStandbyElector.java:809)
            at org.apache.hadoop.ha.ActiveStandbyElector.processResult(ActiveStandbyElector.java:418)
            at org.apache.zookeeper.ClientCnxn$EventThread.processEvent(ClientCnxn.java:599)
            at org.apache.zookeeper.ClientCnxn$EventThread.run(ClientCnxn.java:498)
    Caused by: java.io.FileNotFoundException: /home/hadoop/.ssh/id_rsa (No such file or directory)
            at java.io.FileInputStream.open(Native Method)
            at java.io.FileInputStream.<init>(FileInputStream.java:146)
            at java.io.FileInputStream.<init>(FileInputStream.java:101)
            at com.jcraft.jsch.IdentityFile.newInstance(IdentityFile.java:83)
            ... 14 more

检查发现 NameNode2 服务器未创建 ssh key，使用命令 ssh-keygen -t rsa 命令创建，再次测试，出现如下报错信息：

    2017-04-05 15:12:23,351 WARN org.apache.hadoop.ha.SshFenceByTcpPort: Unable to connect to frin-namenode1 as user hadoop
    com.jcraft.jsch.JSchException: Auth fail
            at com.jcraft.jsch.Session.connect(Session.java:452)
            at org.apache.hadoop.ha.SshFenceByTcpPort.tryFence(SshFenceByTcpPort.java:100)
            at org.apache.hadoop.ha.NodeFencer.fence(NodeFencer.java:97)
            at org.apache.hadoop.ha.ZKFailoverController.doFence(ZKFailoverController.java:532)
            at org.apache.hadoop.ha.ZKFailoverController.fenceOldActive(ZKFailoverController.java:505)
            at org.apache.hadoop.ha.ZKFailoverController.access$1100(ZKFailoverController.java:61)
            at org.apache.hadoop.ha.ZKFailoverController$ElectorCallbacks.fenceOldActive(ZKFailoverController.java:892)
            at org.apache.hadoop.ha.ActiveStandbyElector.fenceOldActive(ActiveStandbyElector.java:910)
            at org.apache.hadoop.ha.ActiveStandbyElector.becomeActive(ActiveStandbyElector.java:809)
            at org.apache.hadoop.ha.ActiveStandbyElector.processResult(ActiveStandbyElector.java:418)
            at org.apache.zookeeper.ClientCnxn$EventThread.processEvent(ClientCnxn.java:599)
            at org.apache.zookeeper.ClientCnxn$EventThread.run(ClientCnxn.java:498)

配置 NameNode2 到 NameNode1 SSH 免密钥登陆后，自动切换成功。