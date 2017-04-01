title: Zookeeper 集群搭建
tags:
  - Zookeeper
categories:
  - 大数据
  - Zookeeper
date: 2017-04-01 15:05:04
---

### hosts 配置

在 /etc/hosts 配置文件中添加如下内容：

    10.142.165.40 frin-zookeeper1
    10.142.165.41 frin-zookeeper2
    10.142.165.44 frin-zookeeper3

<!-- more -->

### 创建用户及目录

- 创建 zookeeper 用户，用来启动 zookeeper 进程。
- 创建 /frin/zookeeper 目录作为 zookeeper 的 home 目录。修改该目录的属主与组为 zookeeper。

### 下载 zookeeper 并解压

当前最新稳定版本是 zookeeper-3.4.10.tar.gz，下载地址：http://www.apache.org/dyn/closer.cgi/zookeeper/。下载并解压到 /frin/zookeeper 目录下。

创建软连接：

    ln -s /frin/zookeeper/zookeeper-3.4.10 /usr/local/zookeeper-3.4.10

### Java heap 设置

设置Java heap 大小，避免内存与磁盘空间的交换，能够大大提升ZK的性能，设置合理的heap大小则能有效避免此类空间交换的触发。在正式发布上线之前，建议是针对使用场景进行一些压力测试，确保正常运行后内存的使用不会触发此类交换。通常在一个物理内存为4G的机器上，最多设置-Xmx为3G。

因为存储数据量很小，此处使用默认值。

### zookeeper 配置

    cd conf
    cp zoo_sample.cfg zoo.cfg

在 zoo.cfg 中添加如下配置：

    tickTime=2000
    initLimit=10
    syncLimit=5
    dataDir=/data/zookeeper
    clientPort=2181
    server.1=frin-zookeeper1:2888:3888
    server.2=frin-zookeeper2:2888:3888
    server.3=frin-zookeeper3:2888:3888

创建数据目录：/data/zookeeper

### 创建 myid 文件

myid文件中只有一个数字，即一个Server ID。例如，server.1 的myid文件内容就是“1”。注意，请确保每个server的myid文件中id数字不同，并且和server.id=host:port:port中的id一致。另外，id的范围是1~255。

分别在三台机器上的 /data/zookeeper 目录下创建 myid 文件，并设置相应的值。

### 启动 zookeeper

    bin/zkServer.sh start

### 测试

    bin/zkCli.sh -server frin-zookeeper1:2181