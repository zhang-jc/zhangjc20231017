title: hadoop 2.7.3 集群安装
tags:
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-03-03 10:31:39
---

### 服务器信息

#### 服务器列表

- 10.142.166.81：NameNode
- 10.142.166.119：ResourceManager / MapReduce JobHistory Server
- 10.142.165.40 / 10.142.165.41 / 10.142.165.44：DataNode / NodeManager

#### 服务器配置信息

- OS：CentOS 6.6
- 4 cpu
- 8G Mem
- 磁盘目录：
  - /frin：80G
  - /data：1T

<!-- more -->

### 准备工作

#### 创建 hadoop 用户

在所有服务器上创建 hadoop 用户，整个集群使用该用户进行管理。创建用户命令：

    useradd hadoop

#### SSH 免密钥

配置服务器 10.142.166.81 的 hadoop 用户到其他服务器的 SSH 免密钥登陆。

#### 目录权限

修改所有服务器的 /data 目录的属主及组为 hadoop。

在所有服务器上创建 /frin/hadoop 目录，并修改属主及组为 hadoop。命令如下：

    cd /frin
    mkdir hadoop
    chown hadoop hadoop
    chgrp hadoop hadoop

### 安装 Hadoop-2.7.3

#### 下载并解压 Hadoop

此处使用官网发行的二进制运行包。在实际中的集群安装可以自己编译之后再安装。

使用 hadoop 帐号登陆服务器 10.142.166.81，下载并解压 Hadoop：

    cd /frin/hadoop
    wget http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    tar xzvf hadoop-2.7.3.tar.gz

#### 创建软链

为了统一及方便管理，在 /usr/local 下创建 hadoop 软链，并修改软链的属主及组为 hadoop：

    cd /usr/local
    ln -s /letv/hadoop/hadoop-2.7.3 hadoop
    chown -h hadoop hadoop
    chgrp -h hadoop hadoop

#### 设置HADOOP_PREFIX及HADOOP_HOME

使用 root 帐号登陆服务器 10.142.166.81，编辑 /etc/profile，增加以下内容：

    export HADOOP_PREFIX=/usr/local/hadoop
    export HADOOP_HOME=/usr/local/hadoop

编辑完成后，将 /etc/profile 分发到其他服务器。

#### 配置 hosts

使用 root 帐号登陆服务器 10.142.166.81，编辑 /etc/hosts，内容如下：

    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

    10.142.166.81 frin-namenode1
    10.142.166.119 frin-resourcemanager1
    10.142.166.119 frin-jobhistoryserver

    10.142.166.81 vm-10-142-166-81
    10.142.166.119 vm-10-142-166-119
    10.142.165.40 vm-10-142-165-40
    10.142.165.41 vm-10-142-165-41
    10.142.165.44 vm-10-142-165-44

编辑完成后，将 /etc/profile 分发到其他服务器。

#### etc/hadoop/core-site.xml 配置

    <property>
      <name>fs.defaultFS</name>
      <value>hdfs://frin-namenode1:9000</value>
    </property>
    <property>
      <name>io.file.buffer.size</name>
      <value>131072</value>
    </property>

#### etc/hadoop/hdfs-site.xml 配置

##### NameNode 配置

    <!-- configurations for namenode -->
    <property>
      <name>dfs.namenode.name.dir</name>
      <value>/data/dfs/name</value>
    </property>
    <property>
      <name>dfs.hosts</name>
      <value>/usr/local/hadoop/etc/hadoop/slaves</value>
    </property>
    <property>
      <name>dfs.hosts.exclude</name>
      <value>/usr/local/hadoop/etc/hadoop/exclude_hosts</value>
    </property>
    <property>
      <name>dfs.blocksize</name>
      <value>268435456</value>
    </property>
    <property>
      <name>dfs.namenode.handler.count</name>
      <value>30</value>
    </property>

配置 etc/hadoop/slaves 文件内容如下：

    10.142.165.44
    10.142.165.41
    10.142.165.40

创建空文件 etc/hadoop/exclude_hosts。

##### DataNode 配置

    <!-- configurations for datanode -->
    <property>
      <name>dfs.datanode.data.dir</name>
      <value>/data/dfs/data</value>
    </property>

#### etc/hadoop/yarn-site.xml 配置

##### ResourceManager 和  NodeManager 配置

    <!-- configurations for ResourceManager and NodeManager -->
    <property>
      <name>yarn.log-aggregation-enable</name>
      <value>true</value>
    </property>
    <property>
      <name>yarn.nodemanager.remote-app-log-dir</name>
      <value>/data/hadoop/yarn-logs</value>
    </property>

> 集群启动后，在 HDFS 上创建目录 /data/hadoop/yarn-logs，并修改目录权限为 777。

##### ResourceManager 配置

    <!-- configurations for ResourceManager -->
    <property>
      <name>yarn.resourcemanager.webapp.address</name>
      <value>frin-resourcemanager1:8088</value>
    </property>
    <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>frin-resourcemanager1</value>
    </property>
    <property>
      <name>yarn.resourcemanager.scheduler.class</name>
      <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler</value>
    </property>
    <property>
      <name>yarn.scheduler.minimum-allocation-mb</name>
      <value>1024</value>
    </property>
    <property>
      <name>yarn.scheduler.maximum-allocation-mb</name>
      <value>8192</value>
    </property>
    <property>
      <name>yarn.resourcemanager.nodes.include-path</name>
      <value>/usr/local/hadoop/etc/hadoop/nodemanagers</value>
    </property>

##### NodeManager 配置

    <!-- configurations for NodeManager -->
    <property>
      <name>yarn.nodemanager.resource.memory-mb</name>
      <value>6144</value>
    </property>
    <property>
      <name>yarn.nodemanager.vmem-pmem-ratio</name>
      <value>1.8</value>
    </property>
    <property>
      <name>yarn.nodemanager.local-dirs</name>
      <value>/data/yarn/data</value>
    </property>
    <property>
      <name>yarn.nodemanager.log-dirs</name>
      <value>/data/yarn/log</value>
    </property>
    <property>
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
    </property>

##### History Server 配置

    <!-- configurations for History Server -->
    <property>
      <name>yarn.log-aggregation.retain-seconds</name>
      <value>86400</value>
    </property>
    <property>
      <name>yarn.log-aggregation.retain-check-interval-seconds</name>
      <value>1800</value>
    </property>

#### etc/hadoop/mapred-site.xml 配置

##### MapReduce Applications 配置

    <!-- configurations for MapReduce Applications -->
    <property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
    </property>
    <property>
      <name>mapreduce.map.memory.mb</name>
      <value>2048</value>
    </property>
    <property>
      <name>mapreduce.map.java.opts</name>
      <value>-Xmx1024M</value>
    </property>
    <property>
      <name>mapreduce.reduce.memory.mb</name>
      <value>4096</value>
    </property>
    <property>
      <name>mapreduce.reduce.java.opts</name>
      <value>-Xmx2048M</value>
    </property>

##### MapReduce JobHistory Server 配置

    <!-- configurations for MapReduce JobHistory Server -->
    <property>
      <name>mapreduce.jobhistory.address</name>
      <value>frin-jobhistoryserver:10020</value>
    </property>
    <property>
      <name>mapreduce.jobhistory.webapp.address</name>
      <value>frin-jobhistoryserver:19888</value>
    </property>

#### NodeManager 健康状态检测

##### 检测脚本

在 bin 目录下创建 nodemanager-health-checker.sh，内容如下：

    #!/bin/bash
    cd `dirname $0`
    cat STATUS

##### 检测配置

在 etc/hadoop/yarn-site.xml 中增加以下配置：

    <!-- configurations for Monitoring Health of NodeManager -->
    <property>
      <name>yarn.nodemanager.health-checker.script.path</name>
      <value>/usr/local/hadoop/bin/nodemanager-health-checker.sh</value>
    </property>
    <property>
      <name>yarn.nodemanager.health-checker.script.interval-ms</name>
      <value>300000</value>
    </property>

##### 分发 Hadoop

将已经配置好的 hadoop 整个目录分发到所有服务器。

### Hadoop 集群启动

#### 格式化 HDFS

使用 hadoop 帐号登陆服务器 10.142.166.81，用以下命令格式化一个新的 HDFS 系统：

     bin/hdfs namenode -format

#### 启动 NameNode

使用 hadoop 帐号登陆服务器 10.142.166.81，用以下命令启动 NameNode：

    sbin/hadoop-daemon.sh start namenode

#### 启动 DataNode

在 slaves 中所有的服务器上用以下命令启动 DataNode：

    sbin/hadoop-daemon.sh start datanode

#### 启动 ResourceManager

用 hadoop 帐号登陆服务器 10.142.166.119，用以下命令启动 Resourcemanager：

    sbin/yarn-daemon.sh start resourcemanager

#### 启动 NodeManager

在 slaves 中所有的服务器上用以下命令启动 NodeManager

    sbin/yarn-daemon.sh start nodemanager

#### 启动 MapReduce JobHistory Server

使用 hadoop 帐号登陆服务器 10.142.166.119，用以下命令启动 MapReduce JobHistory Server

    sbin/mr-jobhistory-daemon.sh start historyserver

### 集群状态检查

将 hosts 配置添加到本地配置文件。

#### NameNode

访问网址：<http://frin-namenode1:50070/>。看到如下信息，说明服务正常：![NameNode](/uploads/20170303/namenode.png)

#### ResourceManager

访问网址：<http://frin-resourcemanager1:8088/>。看到如下信息，说明服务正常：![ResourceManager](/uploads/20170303/resourcemanager.png)

#### MapReduce JobHistory Server

访问网址：<http://frin-jobhistoryserver:19888/>。如果有 Job 运行，则界面如下：![MapReduce JobHistory Server](/uploads/20170303/mapreduce-jobhistory-server.png)