title: Hadoop：安装单个节点的集群
tags:
- 大数据
- Hadoop
categories:
- 大数据
- Hadoop
---
### 目的

本文档描述如何安装并配置一个单节点 Hadoop，因此可以使用 Hadoop MapReduce 和分布式文件系统（HDFS）快速执行简单的操作。

### 前提

#### 支持的平台

- GNU/Linux 作为开发和产品平台都被支持。Hadoop 在 2000 台节点的集群上已经被证明。
- Windows 也是一个被支持的平台，但下面的步骤只针对 Linux。在 Windows 上安装 Hadoop，参见 [wiki 页](http://wiki.apache.org/hadoop/Hadoop2OnWindows)

#### 必须的软件

Linux 必须的软件包括：

1. 必须安装 Java™。[HadoopJavaVersions](http://wiki.apache.org/hadoop/HadoopJavaVersions) 中列出了推荐的 Java 版本。
2. 必须安装 ssh 并且必须运行 sshd 来使用管理远程 Hadoop 守护进程的 Hadoop 脚本。

#### 安装软件

如果集群没有必需的软件则需要安装。在 Ubuntu Linux 上的示例：

    $ sudo apt-get install ssh
    $ sudo apt-get install rsync

### 下载

要获取一个 Hadoop 的发行包，从 [Apache 下载镜像列表](http://www.apache.org/dyn/closer.cgi/hadoop/common/)中的一个下载当前稳定发布版。

### 准备启动 Hadoop 集群

解包下载的 Hadoop 发行包。在发行包中，编辑文件 etc/hadoop/hadoop-env.sh 定义下面的一些参数：

    # set to the root of your Java installation
    export JAVA_HOME=/usr/java/latest

尝试下面的命令：

    $ bin/hadoop

这个命令将展示 hadoop 脚本的使用文档。

现在可以以支持的三种模式中的一种启动 Hadoop 集群：

- 本地单例模式
- 伪分布式模式
- 完全分布式模式

### 单例操作

默认的，Hadoop 配置为以非分布式模式的单个 Java 进程运行。这对调试非常有帮助。

下面的示例拷贝 Hadoop 安装目录下的配置文件作为输入，然后找出并展示所有匹配给定正则表达式的内容。输出内容写入指定的输出目录。

    $ mkdir input
    $ cp etc/hadoop/*.xml input
    $ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'dfs[a-z.]+'
    $ cat output/*

### 伪分布式操作

Hadoop 也可以在单个节点上以伪分布式模式运行，每个 Hadoop 守护进程在一个独立的 Java 进程中运行。

#### 配置

使用以下配置：

etc/hadoop/core-site.xml:

    <configuration>
      <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
      </property>
    </configuration>

etc/hadoop/hdfs-site.xml:
