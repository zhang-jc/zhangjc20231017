title: Hadoop 集群安装
tags:
  - 大数据
  - Hadoop
categories:
  - 大数据
  - Hadoop
---
### 目旳

这篇文档描述如何安装并配置规模从几台节点到上千台节点的集群。为了练习 Hadoop，可以先在单台机器上安装（参见[Hadoop：安装单个节点的集群](http://zhang-jc.github.io/2016/06/24/Hadoop%EF%BC%9A%E5%AE%89%E8%A3%85%E5%8D%95%E4%B8%AA%E8%8A%82%E7%82%B9%E7%9A%84%E9%9B%86%E7%BE%A4/)。

这篇文档不含盖像[安全](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SecureMode.html)或高可用等高级主题。

### 前提

- 安装 Java。查阅 [Hadoop Wiki](http://wiki.apache.org/hadoop/HadoopJavaVersions) 获取已知的适合版本。
- 从 Apache 镜像站点下载 Hadoop 稳定版。

### 安装

安装 Hadoop 集群通常在集群所有机器上解包软件或者通过对应你的操作系统的包管理系统安装。重要的是分割硬件给不同的功能模块。

通常集群中的一台机器专门设计为 NameNode，另外一台机器专门作为 ResourceManager。这些是主节点。其他服务（例如 Web 应用代理服务器和 MapReduce 任务历史服务器）通常既可以运行在专门的硬件上也可以运行在共享的基础设施上，视负载而定。

集群中其他机器同时作为 DataNode 和 NodeManager。这些是从节点。
