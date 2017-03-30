title: Zookeeper 单机操作
tags:
  - Zookeeper
categories:
  - 大数据
  - Zookeeper
date: 2017-03-29 00:02:02
---

### 下载

从 Apache 下载镜像站点下载当前稳定[发布版](http://zookeeper.apache.org/releases.html)。

### 单机操作

安装单机模式的 Zookeeper 服务器是简单明了的。服务器包含在一个单独的 JAR 文件中，因此安装包含创建配置。

<!-- more -->

下载稳定的 ZooKeeper 发布版后，解压并切换到包的根目录。

启动 ZooKeeper 前需要一个配置文件。下面是示例配置文件，创建 conf/zoo.cfg：

    # The number of milliseconds of each tick
    tickTime=2000
    # The number of ticks that the initial 
    # synchronization phase can take
    initLimit=10
    # The number of ticks that can pass between 
    # sending a request and getting an acknowledgement
    syncLimit=5
    # the directory where the snapshot is stored.
    # do not use /tmp for storage, /tmp here is just 
    # example sakes.
    dataDir=/data/zookeeper
    # the port at which the clients will connect
    clientPort=2181

这个配置文件可以叫任意名字，在此我们叫它 conf/zoo.cfg。创建目录 /data/zookeeper，并修改 dataDir 为该目录。

**tickTime**

Zookeeper 使用的单位为毫秒的基本时间。用来做心跳，会话超时的最小时间是两次 tickTime。

**dataDir**

目录位置用来保存内存数据库快照及数据库更新的事务日志，除非另有规定。

**clientPort**

监听客户端连接的端口号。

创建配置文件后就可以启动  ZooKeeper 了：

    bin/zkServer.sh start

ZooKeeper 用 log4j 记录日志消息 -- 可以在程序员指导手册中的 [Logging](http://zookeeper.apache.org/doc/trunk/zookeeperProgrammers.html#Logging) 一节获取更多详细信息。可以从控制台（默认）并且 / 或者看到日志信息，这依赖 log4j 的配置。

这里列出的步骤是运行单机模式 ZooKeeper 的。单机模式没有副本，因此，如果 ZooKeeper 进程失败，则服务就会宕掉。这对于大多数开发场景是没问题的，运行副本模式的 ZooKeeper，请参见[运行副本模式的 Zookeeper](http://zookeeper.apache.org/doc/trunk/zookeeperStarted.html#sc_RunningReplicatedZooKeeper)。

### 管理 ZooKeeper 存储

对于长期运行的生产系统，ZooKeeper 存储必须从外部进行管理（dataDir 和 logs）。参见在 [maintenance](http://zookeeper.apache.org/doc/trunk/zookeeperAdmin.html#sc_maintenance) 中的章节获取更多信息。

### 连接到 ZooKeeper

    bin/zkCli.sh -server 127.0.0.1:2181

这使你可以执行简单的、像文件一样的操作。

当建立了连接，应该可以看到像下面的一些信息：

    Connecting to localhost:2181
    log4j:WARN No appenders could be found for logger (org.apache.zookeeper.ZooKeeper).
    log4j:WARN Please initialize the log4j system properly.
    Welcome to ZooKeeper!
    JLine support is enabled
    [zkshell: 0]

在 shell 中，输入 help 获取可以从客户端执行的命令列表，像下面：

    help
    ZooKeeper -server host:port cmd args
            connect host:port
            get path [watch]
            ls path [watch]
            set path data [version]
            rmr path
            delquota [-n|-b] path
            quit 
            printwatches on|off
            create [-s] [-e] path data acl
            stat path [watch]
            close 
            ls2 path [watch]
            history 
            listquota path
            setAcl path acl
            getAcl path
            sync path
            redo cmdno
            addauth scheme auth
            delete path [version]
            setquota -n|-b val path

从这里，可以尝试几个简单的命令来体验简单的命令行接口。首先，从命令列表开始，像 ls：

    [zk: 127.0.0.1:2181(CONNECTED) 1] ls /
    [zookeeper]

接下来，通过运行 create /zk_test my_data 创建一个新的 znode。这会创建一个新的 znode 并与这个节点关联字符串“my_data”：

    [zk: 127.0.0.1:2181(CONNECTED) 2] create /zk_test my_data
    Created /zk_test

输入 ls / 命令查看目录结构：

    [zk: 127.0.0.1:2181(CONNECTED) 3] ls /
    [zookeeper, zk_test]

现在 zk_test 目录被创建了。

接下来，通过 get 命令确认数据已经与 znode 关联，像：

    [zk: 127.0.0.1:2181(CONNECTED) 4] get /zk_test
    my_data
    cZxid = 0x2
    ctime = Tue Mar 28 23:37:16 CST 2017
    mZxid = 0x2
    mtime = Tue Mar 28 23:37:16 CST 2017
    pZxid = 0x2
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 7
    numChildren = 0

可以输入 set 命令修改与 zk_test 关联的数据，像：

    [zk: 127.0.0.1:2181(CONNECTED) 5] set /zk_test junk
    cZxid = 0x2
    ctime = Tue Mar 28 23:37:16 CST 2017
    mZxid = 0x3
    mtime = Tue Mar 28 23:46:12 CST 2017
    pZxid = 0x2
    cversion = 0
    dataVersion = 1
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 4
    numChildren = 0
    [zk: 127.0.0.1:2181(CONNECTED) 6] get /zk_test
    junk
    cZxid = 0x2
    ctime = Tue Mar 28 23:37:16 CST 2017
    mZxid = 0x3
    mtime = Tue Mar 28 23:46:12 CST 2017
    pZxid = 0x2
    cversion = 0
    dataVersion = 1
    aclVersion = 0
    ephemeralOwner = 0x0
    dataLength = 4
    numChildren = 0

最后，输入以下命令删除节点：

    [zk: 127.0.0.1:2181(CONNECTED) 7] delete /zk_test
    [zk: 127.0.0.1:2181(CONNECTED) 8] ls /
    [zookeeper]