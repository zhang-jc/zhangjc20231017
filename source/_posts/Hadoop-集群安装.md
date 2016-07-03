title: Hadoop 集群安装
tags:
  - 大数据
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2016-07-02 22:33:29
---

### 目旳

这篇文档描述如何安装并配置规模从几台节点到上千台节点的集群。为了练习 Hadoop，可以先在单台机器上安装（参见[Hadoop：安装单个节点的集群](http://zhang-jc.github.io/2016/06/24/Hadoop%EF%BC%9A%E5%AE%89%E8%A3%85%E5%8D%95%E4%B8%AA%E8%8A%82%E7%82%B9%E7%9A%84%E9%9B%86%E7%BE%A4/))。

这篇文档不含盖像[安全](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SecureMode.html)或高可用等高级主题。

### 前提

- 安装 Java。查阅 [Hadoop Wiki](http://wiki.apache.org/hadoop/HadoopJavaVersions) 获取已知的适合版本。
- 从 Apache 镜像站点下载 Hadoop 稳定版。

### 安装

安装 Hadoop 集群通常在集群所有机器上解包软件或者通过对应你的操作系统的包管理系统安装。重要的是分割硬件给不同的功能模块。

通常集群中的一台机器专门设计为 NameNode，另外一台机器专门作为 ResourceManager。这些是主节点。其他服务（例如 Web 应用代理服务器和 MapReduce 任务历史服务器）通常既可以运行在专门的硬件上也可以运行在共享的基础设施上，视负载而定。

集群中其他机器同时作为 DataNode 和 NodeManager。这些是从节点。

### 配置 Hadoop 非安全模式

Hadoop 的 Java 配置设计为两种重要的配置文件：

- 只读的默认配置文件：core-default.xml、hdfs-default.xml、yarn-default.xml 和 mapred-default.xml。
- 站点特定配置文件：etc/hadoop/core-site.xml、etc/hadoop/hdfs-site.xml、etc/hadoop/yarn-site.xml 和 etc/hadoop/mapred-site.xml。

另外，通过 etc/hadoop/hadoop-env.sh 和 etc/hadoop/yarn-env.sh 设置站点特定值可以控制 bin 目录下的 Hadoop 脚本。

为了配置 Hadoop 集群，需要配置 Hadoop 守护进程执行的环境以及参数。

HDFS 守护进程是 NameNode、SecondaryNameNode 和 DataNode。YARN 守护进程是 ResourceManager、NodeManager 和 WebAppProxy。如果要使用 MapReduce，则 MapReduce 作业历史记录服务器也将运行。对于大型集群安装，这些通常都运行在独立主机上。

#### 配置 Hadoop 守护进程的环境

管理员需要使用 etc/hadoop/hadoop-env.sh 以及可选的 etc/hadoop/mapred-env.sh 和 etc/hadoop/yarn-env.sh 脚本来设置 Hadoop 守护进程环境的个性化站点特定参数。

至少必需指定 JAVA_HOME，这样才能在每个远程节点上正确的定义。

管理员可以使用下面表格中展示的配置项配置每个独立的守护进程：

| 守护进程                       | 环境变量                        |
| :---------------------------- | :---------------------------- |
| NameNode                      | HADOOP_NAMENODE_OPTS          |
| DataNode                      | HADOOP_DATANODE_OPTS          |
| Secondary NameNode            | HADOOP_SECONDARYNAMENODE_OPTS |
| ResourceManager               | YARN_RESOURCEMANAGER_OPTS     |
| NodeManager                   | YARN_NODEMANAGER_OPTS         |
| WebAppProxy                   | YARN_PROXYSERVER_OPTS         |
| Map Reduce Job History Server | HADOOP_JOB_HISTORYSERVER_OPTS |

例如，配置 Namenode 使用并行垃圾回收，需要在 hadoop-env.sh 中添加下面的语句：

    export HADOOP_NAMENODE_OPTS="-XX:+UseParallelGC"

查看 etc/hadoop/hadoop-env.sh 获取另外一个示例。

其他可以定制的有用配置参数包括：

- HADOOP_PID_DIR － 守护进程 ID 文件存放的目录。
- HADOOP_LOG_DIR － 守护进程日志文件存放的目录。如果日志文件不存在会被自动创建。
- HADOOP_HEAPSIZE / YARN_HEAPSIZE － 可使用的最大堆内存大小（单位：MB），例如参数设置为 1000 则堆被设置为 1000MB。这个是用来设置守护进程堆大小的。默认值为 1000。可以用来为每个守护进程单独设置。

在大多数情况下，需要指定运行 Hadoop 守护进程的用户可以写入的 HADOOP_PID_DIR 和 HADOOP_LOG_DIR 目录。否则存在符号链接攻击的可能性。

在全系统 Shell 环境配置中通常也配置 HADOOP_PREFIX。例如，在 /etc/profile.d 内一个简单的脚本：

    HADOOP_PREFIX=/path/to/hadoop
    export HADOOP_PREFIX

| 守护进程                       | 环境变量                           |
| :---------------------------- | :-------------------------------- |
| ResourceManager               | YARN_RESOURCEMANAGER_HEAPSIZE     |
| NodeManager                   | YARN_NODEMANAGER_HEAPSIZE         |
| WebAppProxy                   | YARN_PROXYSERVER_HEAPSIZE         |
| Map Reduce Job History Server | HADOOP_JOB_HISTORYSERVER_HEAPSIZE |

#### 配置 Hadoop 守护进程

这一节处理指定配置文件中的重要参数：

##### etc/hadoop/core-site.xml

| 参数                | 值            | 备注                             |
| :------------------ | :----------- | :------------------------------ |
| fs.defaultFS        | NameNode URI | hdfs://host:port/               |
| io.file.buffer.size | 131072       | 读/写 SequenceFile 使用的缓冲大小。 |

##### etc/hadoop/hdfs-site.xml

- 配置 NameNode：

| 参数 | 值 | 备注 |
| :---------------------------- | :--------------------------------------- | :-------------------------------------- |
| dfs.namenode.name.dir | 永久保存 NameNode 命名空间和事务日志的本地文件系统路径。 | 如果是一个逗号分隔的目录列表，那么名称表格会冗余的在所有目录中复制一份。 |
| dfs.hosts / dfs.hosts.exclude | 被允许的 / 排除的 DataNode 列表             | 如果必要，使用这些文件控制允许的 DataNode 列表。 |
| dfs.blocksize | 268435456     | 大文件系统 HDFS 块大小为 256MB。            |
| dfs.namenode.handler.count    | 100                                      | 更多 NameNode 服务器线程来处理从大量 DataNode 来的 RPC 请求。 |

- 配置 DataNode：

| 参数 | 值    | 备注                             |
| :------------------ | :----------- | :------------------------------ |
| dfs.datanode.data.dir | DataNode 存储数据块的逗号分隔的本地文件系统的路径列表。 | 如果是一个逗号分隔的目录列表，数据会存储在所有命名的目录下，通常在不同的设备上。 |

##### etc/hadoop/yarn-site.xml

- 设置 ResourceManager 和 NodeManager：

| 参数 | 值    | 备注                             |
| :------------------ | :----------- | :------------------------------ |
| yarn.acl.enable | true / false | 启用 ACL？默认为 false。 |
| yarn.admin.acl | Admin ACL | 设置集群管理的 ACL。ACL 格式是：user1,user2 group1,group2。默认为特殊字符 *，意思是任何人。只有一个空格的特殊值意味着没有人可以访问。 |
| yarn.log-aggregation-enable | false | 配置启用或禁用日志聚合 |

- 设置 ResourceManager：

| 参数 | 值    | 备注                             |
| :------------------ | :----------- | :------------------------------ |
| yarn.resourcemanager.address | 客户端提交作业的 ResourceManager 的 host:port | 如果设置了 host:port，会重写 yarn.resourcemanager.hostname 设置的主机名 |
| yarn.resourcemanager.scheduler.address | ApplicationMasters 告诉 Scheduler 获取资源的 ResourceManager 的 host:port | 如果设置了 host:port，会重写 yarn.resourcemanager.hostname 设置的主机名 |
| yarn.resourcemanager.resource-tracker.address | NodeManager 的 ResourceManager 的 host:port | 如果设置了 host:port，会重写 yarn.resourcemanager.hostname 设置的主机名 |
| yarn.resourcemanager.admin.address | 管理命令使用的 ResourceManager 的 host:port | 如果设置了 host:port，会重写 yarn.resourcemanager.hostname 设置的主机名 |
| yarn.resourcemanager.webapp.address | ResourceManager Web 用户界面的 host:port | 如果设置了 host:port，会重写 yarn.resourcemanager.hostname 设置的主机名 |
| yarn.resourcemanager.hostname | ResourceManager 主机 | 用来替换所有资源设置项 yarn.resourcemanager*address 的单独主机名。启用这个设置 ResourceManager 组件使用默认端口。 |
| yarn.resourcemanager.scheduler.class | ResourceManager Scheduler 类 | CapacityScheduler（推荐），FairScheduler（也推荐）或者 FifoScheduler |
| yarn.scheduler.minimum-allocation-mb | 在 ResourceManager 中分配给每个容器请求的内存的最小限制。 | 单位：MB |
| yarn.scheduler.maximum-allocation-mb | 在 ResourceManager 中分配给每个容器请求的内存的最大限制。 | 单位：MB |
| yarn.resourcemanager.nodes.include-path / yarn.resourcemanager.nodes.exclude-path | 允许的 / 排除的 NodeManager 列表 | 如果需要，使用这些文件控制允许的 NodeManager 列表。 |

- 设置 NodeManager：

| 参数 | 值    | 备注 |
| :------------------ | :----------- | :------------------------------ |
| yarn.nodemanager.resource.memory-mb | 给 NodeManager 的资源，如可获取的物理内存，单位 MB。 | 定义了 NodeManager 上可以获取的全部资源来运行容器。 |
| yarn.nodemanager.vmem-pmem-ratio | 任务的虚拟内存使用可能超过物理内存的最大比例。 | 通过配置该参数每个任务使用的虚拟内存可能超过它可以使用的物理内存限制。通过这个参数 NodeManager 上的任务使用的虚拟内存总量可能超过它的物理内存。 |
| yarn.nodemanager.local-dirs | 逗号分隔的本地文件系统的路径列表，用来写入中间结果。 | 多个路径帮助提高磁盘 I／O。 |
| yarn.nodemanager.log-dirs | 逗号分隔的本地文件系统的路径列表，用来写入日志。 | 多个路径帮助提高磁盘 I／O。 |
| yarn.nodemanager.log.retain-seconds | 10800 | 如果禁用日志收集，在 NodeManager 上适当的保存日志文件的默认时间（单位：秒）。 |
| yarn.nodemanager.remote-app-log-dir | /logs | HDFS 的目录，应用程序完成时会将日志移动到这个目录。需要设置合适的权限。只有启用 log-aggregation 该设置才可用。 |
| yarn.nodemanager.remote-app-log-dir-suffix | 追加到远程日志目录的后缀。日志将被收集到 ${yarn.nodemanager.remote-app-log-dir}/${user}/${thisParam} 下。只有启用 log-aggregation 该设置才可用。 |
| yarn.nodemanager.aux-services | mapreduce_shuffle | Map Reduce 应用程序需要设置的 Shuffle 服务。 |

- 设置 History Server（如果需要移动到其他地方）：

| 参数 | 值 | 备注 |
| :------------------ | :----------- | :------------------------------ |
| yarn.log-aggregation.retain-seconds | -1 | 收集的日志在删除前保留多长时间。-1 禁用。注意，这个参数设置太小会对 NameNode 发送垃圾信息。 |
| yarn.log-aggregation.retain-check-interval-seconds | -1 | 检查收集日志保留的间隔时间。如果设置为 0 或一个负数，那么值被计算为十分之一的聚合日志保留时间。注意，这个参数设置太小会对 NameNode 发送垃圾信息。 |

##### etc/hadoop/mapred-site.xml

- 设置 MapReduce Applications：

| 参数 | 值 | 备注 |
| :----------------------- | :--- | :------------------------ |
| mapreduce.framework.name | yarn | 执行框架设置为 Hadoop YARN。 |
| mapreduce.map.memory.mb  | 1536 | Map 任务较大的资源限制。      |
| mapreduce.map.java.opts  | -Xmx1024M | Map 任务子虚拟机较大的堆大小。 |
| mapreduce.reduce.memory.mb | 3072 ｜ Reduce 任务较大的资源限制。 |
| mapreduce.reduce.java.opts | -Xmx2560M | Reduce 任务子虚拟机较大的堆大小。 |
| mapreduce.task.io.sort.mb | 512 | 当为了提高数据排序性能设置的较高内存限制。 |
| mapreduce.task.io.sort.factor | 100 | 在排序文件时，多个流合并一次。 |
| mapreduce.reduce.shuffle.parallelcopies ｜ 50 ｜ 当 Reduce 从大量 Map 任务获取输出时更高数量的并行拷贝。 |

- 设置 MapReduce JobHistory Server：

| 参数 | 值 | 备注 |
| :--------------------------- | :-------------------------------------- | :-------------- |
| mapreduce.jobhistory.address | MapReduce JobHistory Server 的 host:port | 默认端口是 10020 |
| mapreduce.jobhistory.webapp.address | MapReduce JobHistory Server Web UI 的 host:port | 默认端口是 19888 |
| mapreduce.jobhistory.intermediate-done-dir | /mr-history/tmp | MapReduce 作业写入历史文件的目录 |
| mapreduce.jobhistory.done-dir | /mr-history/done | MR JobHistory Server 管理历史文件的目录 |

### 监测 NodeManager 的健康状态：　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　
Hadoop 提供了一种机制，管理员可以配置 NodeManager 周期性运行管理脚本来检测一个节点是否健康。

管理员通过执行任何他们选择的脚本中的检查来监测节点是否在健康状态。如果脚本监测到节点处于异常状态，它必须向标准输出打印一行以 ERROR 开头的字符串。NodeManager 周期性调用脚本并检查它的输出。如果脚本的输出包含字符串 ERROR，像上面描述的，节点的状态报告为 *不健康* 并且这个节点会被 ResourceManager 列入黑名单。不会再有任务指派到这个节点。然而，NodeManager 会继续执行脚本，因此如果节点再次变为健康的，它会自动被 ResourceManager 从黑名单中移除。节点的健康状态随着脚本的输出，如果节点不健康，管理员可以在 ResourceManager Web 界面获得。当节点变为健康的时候也可以在 Web 界面看到。

下面在 etc/hadoop/yarn-site.xml 中的参数可以用来控制节点健康监测脚本：

| 参数 | 值 | 备注 |
| :--------------------------- | :-------------------------------------- | :-------------- |
| yarn.nodemanager.health-checker.script.path | 节点健康监测脚本 | 节点健康状态检查脚本 |
| yarn.nodemanager.health-checker.script.opts | 节点健康监测脚本选项 | 节点健康状态检查脚本选项 |
| yarn.nodemanager.health-checker.script.interval-ms | 节点健康监测脚本执行间隔 | 执行健康监测脚本的间隔 |
| yarn.nodemanager.health-checker.script.timeout-ms | 节点健康监测脚本超时时长 | 节点健康监测脚本执行超时时长 |

健康检查脚本不应该给错误，如果只有一些本地磁盘坏了。NodeManager 有周期性检查本地磁盘健康的能力（特别是检查 nodemanager-local-dirs 和 nodemanager-log-dirs）并且坏掉的目录数量达到 yarn.nodemanager.disk-health-checker.min-healthy-disks 属性设置的值，整个节点被标示为不健康并且这个信息被发送给资源管理器。引导磁盘要么做磁盘阵列，要么通过健康检查脚本识别引导磁盘失败。

### Slaves 文件

在 etc/hadoop/slaves 文件中列出所有从节点的主机名或者 IP，在一行中。帮助脚本（下面描述的）将使用 etc/hadoop/slaves 一次性在很多主机上执行命令。它不会用在任何基于 Java 的 Hadoop 配置。为了用这个功能，必须为运行 Hadoop 的账号建立 ssh 信任（通过免密码 ssh 或者其他方式，如 Kerberos）。

### Hadoop 机架意识

很多 Hadoop 组件是机架感知的并且利用网络拓扑提高性能和安全。Hadoop 守护进程通过调用一个管理员配置模块获取集群中从节点的机架信息。查看[机架意识](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/RackAwareness.html)获取更多详细信息。

强烈推荐配置机架意识优先来启动 HDFS。

### 日志

Hadoop 用 [Apache log4j](http://logging.apache.org/log4j/2.x/) 通过 Apache Commons Logging 框架来记录日志。编辑 etc/hadoop/log4j.properties 定制 Hadoop 守护进程的日志配置（日志格式等）。

### 操作 Hadoop 集群

当所有必须的配置完成后，分发所有配置文件到所有机器上的 HADOOP_CONF_DIR 目录。这应该是在所有机器上相同的目录。

通常，推荐 HDFS 和 YARN 作为分别独立的用户运行。在大多数安装中，HDFS 进程执行为 hdfs。YARN 通常使用 yarn 帐号。

#### 启动 Hadoop

启动 Hadoop 集群需要启动 HDFS 和 YARN 集群。

第一次启动 HDFS，必须先格式化。用 hdfs 帐号格式化一个新的分布式文件系统：

    [hdfs]$ $HADOOP_PREFIX/bin/hdfs namenode -format <cluster_name>

用 hdfs 帐号用下面的命令在指定的节点上启动 HDFS NameNode：

    [hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs start namenode

用 hdfs 帐号用下面的命令在每个指定的节点上启动 HDFS DataNode：

    [hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemons.sh --config $HADOOP_CONF_DIR --script hdfs start datanode

如果 etc/hadoop/slaves 和 ssh 信任登录被配置（查看 [Single Node Setup](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html))，所有的 HDFS 进程可以用一个工具脚本启动。使用 hdfs 帐号：

    [hdfs]$ $HADOOP_PREFIX/sbin/start-dfs.sh

用下面的命令启动 YARN，用 yarn 帐号在指定的 ResourceManager 上运行：

    [yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR start resourcemanager

用 yarn 帐号在每个指定的节点上运行一个脚本启动 NodeManager：

    [yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemons.sh --config $HADOOP_CONF_DIR start nodemanager

启动一个单独的 WebAppProxy 服务器。在 WebAppProxy 服务器上用 yarn 帐号运行。如果用多台服务器来做负载均衡，那么应该在它们每个上面运行：

    [yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR start proxyserver

如果 etc/hadoop/slaves 和 ssh 信任登录被配置（查看 [Single Node Setup](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html))，所有的 YARN 进程可以用一个工具脚本启动。使用 yarn 帐号：

    [yarn]$ $HADOOP_PREFIX/sbin/start-yarn.sh

用下面的命令启动 MapReduce 作业历史服务器，在指定的服务器上用 mapred 帐号：

    [mapred]$ $HADOOP_PREFIX/sbin/mr-jobhistory-daemon.sh --config $HADOOP_CONF_DIR start historyserver

#### 关闭 Hadoop

用下面的命令停止 NameNode，在指定的 NameNode 上用帐号 hdfs 执行：

    [hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemon.sh --config $HADOOP_CONF_DIR --script hdfs stop namenode

用 hdfs 帐号执行一个脚本停止 DataNode：

    [hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemons.sh --config $HADOOP_CONF_DIR --script hdfs stop datanode

如果 etc/hadoop/slaves 和 ssh 信任登录被配置（查看 [Single Node Setup](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html))，所有的 HDFS 进程可以用一个工具脚本停止。使用 hdfs 帐号：

    [hdfs]$ $HADOOP_PREFIX/sbin/stop-dfs.sh

用下面的命令停止 ResourceManager，用 yarn 帐号在指定的 ResourceManager 上运行：

    [yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR stop resourcemanager

在从节点上用 yarn 帐号执行一个脚本来停止 NodeManager：

    [yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemons.sh --config $HADOOP_CONF_DIR stop nodemanager

如果 etc/hadoop/slaves 和 ssh 信任登录被配置（查看 [Single Node Setup](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SingleCluster.html))，所有的 YARN 进程可以用一个工具脚本停止。使用 yarn 帐号：

    [yarn]$ $HADOOP_PREFIX/sbin/stop-yarn.sh

停止 WebAppProxy 服务器。用 yarn 帐号在 WebAppProxy 服务器上执行。如果用多台服务器来做负载均衡，那么应该在它们每个上面运行：

    [yarn]$ $HADOOP_YARN_HOME/sbin/yarn-daemon.sh --config $HADOOP_CONF_DIR stop proxyserver

用下面的命令停止 MapReduce 作业历史服务器，在指定的服务器上用 mapred 帐号：

    [mapred]$ $HADOOP_PREFIX/sbin/mr-jobhistory-daemon.sh --config $HADOOP_CONF_DIR stop historyserver

### Web 用户界面

当 Hadoop 集群启动后，像下面描述的运行检查组件的 web-ui：

| 守护进程 | Web 用户界面 | 备注 |
| :--------------------------- | :-------------------------------------- | :-------------- |
| NameNode | http://nn_host:port/ | 默认的 HTTP 端口是 50070 |
| ResourceManager | http://rm_host:port/ | 默认的 HTTP 端口是 8088 |
| MapReduce JobHistory Server | http://jhs_host:port/ | 默认的 HTTP 端口是 19888 |
