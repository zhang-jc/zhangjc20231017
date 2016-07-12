title: Hadoop 集群安装及配置实战
tags:
  - 大数据
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2016-07-12 22:46:10
---

### 目的

在三台机器上安装 Hadoop 集群，并配置启动，且成功运行 WordCount 示例程序。本文只设置了必要的配置。

### 准备工作

#### 机器列表

三台相同配置的虚拟机：192.168.1.133、192.168.1.134、192.168.1.139

三台虚拟机配置互相免密码 SSH 登陆。

#### 机器配置

三台虚拟机配置如下：

- 操作系统：Ubuntu Server 16.04 LTS
- 内存：2G
- 磁盘：15G

#### 架构

- 192.168.1.133：Master 主机，运行 NameNode、ResourceManager、MapReduce 作业历史服务器
- 192.168.1.134、192.168.1.139：Slave 主机，运行 DataNode、NodeManager

### 配置过程

#### 配置 /etc/hosts

在三台机器的 /etc/hosts 中添加以下配置：

    192.168.1.133 master-hadoop
    192.168.1.134 slave1-hadoop
    192.168.1.139 slave2-hadoop

#### 配置 slaves

编辑 etc/hadoop/slaves 文件，内容如下：

    192.168.1.134
    192.168.1.139

#### 设置 JAVA_HOME

修改 etc/hadoop/hadoop-env.sh、etc/hadoop/mapred-env.sh、etc/hadoop/yarn-env.sh 三个配置文件中的 JAVA_HOME 参数。

#### 配置 etc/hadoop/core-site.xml

    <configuration>
      <property>
        <name>fs.defaultFS</name>
        <value>hdfs://master-hadoop:9000</value>
      </property>
    </configuration>

#### 配置 etc/hadoop/hdfs-site.xml

    <configuration>
      <property>
        <name>dfs.replication</name>
        <value>1</value>
      </property>
    </configuration>

#### 配置 etc/hadoop/mapred-site.xml

    <configuration>
      <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
      </property>
      <property>
        <name>mapreduce.jobhistory.address</name>
        <value>master-hadoop:10020</value>
      </property>
      <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>master-hadoop:19888</value>
      </property>
    </configuration>

#### 配置 etc/hadoop/yarn-site.xml

    <configuration>
      <property>
        <name>yarn.resourcemanager.address</name>
        <value>master-hadoop:8032</value>
      </property>
      <property>
        <name>yarn.resourcemanager.scheduler.address</name>
        <value>master-hadoop:8030</value>
      </property>
      <property>
        <name>yarn.resourcemanager.resource-tracker.address</name>
        <value>master-hadoop:8031</value>
      </property>
      <property>
        <name>yarn.resourcemanager.admin.address</name>
        <value>master-hadoop:18033</value>
      </property>
      <property>
        <name>yarn.resourcemanager.webapp.address</name>
        <value>master-hadoop:8088</value>
      </property>
      <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
      </property>
    </configuration>

#### 分发文件

将配置完成的 hadoop 文件包分发到三台虚拟机的 /root 目录下（可以根据自己的情况选择）。

### 启动 Hadoop

#### 格式化 HDFS

在 NameNode 主机上执行以下命令格式化 HDFS：

    hdfs namenode -format

#### 启动 NameNode

登陆 192.168.1.133 节点，执行以下命令启动 NameNode：

    # sbin/hadoop-daemon.sh --script hdfs start namenode

#### 启动 DataNode

在每个 Slave 节点上用以下命令启动 DataNode：

    # sbin/hadoop-daemons.sh --script hdfs start datanode

> 注：可以直接使用下面的命令启动 NameNode 和 DataNode，因为我们已经配置了 slaves 和 免密码登陆。

    # sbin/start-dfs.sh

#### 启动 ResourceManager

登陆 192.168.1.133 ，执行以下命令启动 ResourceManager：

    # sbin/yarn-daemon.sh start resourcemanager

#### 启动 NodeManager

在每个 Slave 节点上启动 NodeManager：

    # sbin/yarn-daemons.sh start nodemanager

> 注：可以直接使用下面的命令启动 NameNode 和 DataNode，因为我们已经配置了 slaves 和 免密码登陆。

    # sbin/start-yarn.sh

#### 启动 MapReduce 作业历史服务器

登陆 192.168.1.133，执行以下命令启动 MapReduce 作业历史服务器：

### 检查

#### Web 界面

分别打开下面三个 Web 界面检查启动正确性：

| 守护进程 | Web 用户界面 |
| :------ | :---------- |
| NameNode | http://192.168.1.133:50070/ |
| ResourceManager | http://192.168.1.133::8088/ |
| MapReduce JobHistory Server | http://192.168.1.133:19888/ |

#### 遇到的问题

在检查 NameNode 的 Web 界面时，发现 live node 数量为 0，检查及解决步骤如下：

- 检查了 Slave 主机上的 DataNode 都已经启动；
- 检查 192.168.1.134 Slave 主机上 DataNode 启动 log，发现下面的警告信息，说明 DataNode 节点无法连接 NameNode 的 9000 端口：

    2016-07-10 22:41:17,213 WARN org.apache.hadoop.hdfs.server.datanode.DataNode: Problem connecting to server: master-hadoop/192.168.1.133:9000
    2016-07-10 22:41:23,216 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 0 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:24,217 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 1 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:25,219 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 2 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:26,220 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 3 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:27,222 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 4 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:28,223 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 5 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:29,225 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 6 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:30,227 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 7 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:31,229 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 8 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)
    2016-07-10 22:41:32,231 INFO org.apache.hadoop.ipc.Client: Retrying connect to server: master-hadoop/192.168.1.133:9000. Already tried 9 time(s); retry policy is RetryUpToMaximumCountWithFixedSleep(maxRetries=10, sleepTime=1000 MILLISECONDS)

- 检查 NameNode 端口 9000 情况，信息如下所示，说明 NameNode 进程只监听了 127.0.1.1:9000 端口，除了自己之外其他主机都不能连接：

    # netstat -apn|grep 9000
    tcp        0      0 127.0.1.1:9000          0.0.0.0:*               LISTEN      4964/java

- 检查 /etc/hosts 配置文件，发现有如下配置：

    127.0.1.1 master-hadoop

- 从 /etc/hosts 中删除上面一条配置，重启 NameNode，再次检查 NameNode 端口 9000 情况：

    # netstat -apn|grep 9000
    tcp        0      0 192.168.1.133:9000      0.0.0.0:*               LISTEN      1772/java       
    tcp        0      0 192.168.1.133:9000      192.168.1.139:56002     ESTABLISHED 1772/java       
    tcp        0      0 192.168.1.133:9000      192.168.1.134:35168     ESTABLISHED 1772/java

- 再次检查 NameNode 的 Web 界面发现 live node 数量为 2。

### 运行 WordCount 示例程序

    # hdfs dfs -mkdir -p /user/root/input
    # hdfs dfs -put etc/hadoop/*.xml /user/root/input
    # hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep /user/root/input /user/root/output 'dfs[a-z.]+'
    # hdfs dfs -cat /user/root/output/*
    1	dfsadmin
    1	dfs.replication
