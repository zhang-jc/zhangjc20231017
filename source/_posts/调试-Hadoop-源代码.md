title: 调试 Hadoop 源代码
tags:
  - Hadoop
  - Eclipse
categories:
  - 大数据
  - Hadoop
date: 2016-09-11 12:26:51
---

### Hadoop 版本

Hadoop 2.7.3

### 调试模式下启动 Hadoop NameNode

在 ${HADOOP_HOME}/etc/hadoop/hadoop-env.sh 中设置 NameNode 启动的 JVM 参数，如下：

    export HADOOP_NAMENODE_OPTS="-Xdebug -Xrunjdwp:transport=dt_socket,address=8788,server=y,suspend=y"
    export HADOOP_NAMENODE_OPTS="-Dhadoop.security.logger=${HADOOP_SECURITY_LOGGER:-INFO,RFAS} -Dhdfs.audit.logger=${HDFS_AUDIT_LOGGER:-INFO,NullAppender} $HADOOP_NAMENODE_OPTS"

使用脚本 ${HADOOP_HOME}/sbin/start-dfs.sh 启动 HDFS，如果有以下提示信息则说明调试模式下启动 NameNode 成功：

    Listening for transport dt_socket at address: 8788

此时，如果执行 jps 查看 java 进程信息会有以下信息，是因为 NameNode 进程被挂起并处于监听状态，直到收到 debug 确认信息。

    $ jps
    10638 Jps
    10438 -- main class information unavailable
    2171 
    10508 DataNode

### 设置断点

找到 hadoop-hdfs/src/main/java/org/apache/hadoop/hdfs/server/namenode/NameNode.java 并在 main 函数中设置断点，如下图：![NameNode Breakpoints](/uploads/20160911/namenode-breakpoints.png)

### 在 Eclipse 中调试

在 NameNode.java 代码中点击右键，在弹出的菜单中选择 Debug As -> Debug Configurations...，在弹出的对话框中双击 Remote Java Application，配置内容如图：![Remote Java Application Debug Configuration](/uploads/20160911/remote-java-application-debug-conf.png)

配置完成后点击 Debug 按钮进入调试界面。