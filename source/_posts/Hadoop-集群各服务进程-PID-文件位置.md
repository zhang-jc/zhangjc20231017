title: Hadoop 集群各服务进程 PID 文件位置
tags:
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-03-14 15:36:34
---

PID 文件位置默认在 /tmp 下，这个目录中的文件可能被清理，导致 PID 找不到，参见[Hadoop 进程关闭时报 no 进程名 to stop](http://zhang-jc.github.io/2017/02/24/Hadoop-%E8%BF%9B%E7%A8%8B%E5%85%B3%E9%97%AD%E6%97%B6%E6%8A%A5-no-%E8%BF%9B%E7%A8%8B%E5%90%8D-to-stop/)。为了避免这个问题，需要修改 Hadoop 各服务进程 PID 文件的位置：

<!-- more -->

- namenode、datanode、journalnode

etc/hadoop/hadoop-env.sh 中增加：

    export HADOOP_PID_DIR=${HADOOP_HOME}/pids

- resourcemanager、timelineserver

sbin/yarn-daemon.sh 中增加：

    export YARN_PID_DIR=${HADOOP_HOME}/pids

- MR jobhistoryserver

sbin/mr-jobhistory-daemon.sh 中添加：

    export HADOOP_MAPRED_PID_DIR=${HADOOP_HOME}/pids