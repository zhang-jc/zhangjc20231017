title: Hadoop 集群修改服务绑定地址
tags:
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-05-21 11:54:35
---

有时集群服务器会有多个网卡，例如一个网卡用户服务器管理，一个网卡用于数据交换。一般这种情况下，数据交换网是不对终端用户开放的。所以，如果服务进程监听地址绑定到这个地址的话，通过管理地址是无法访问到该服务的。Hadoop 提供了以下配置项，可以将服务监听地址绑定到 0.0.0.0，这样通过任一地址都可以访问到服务了。如下：

**hdfs-site.xml 中的配置项**

- dfs.namenode.rpc-bind-host
- dfs.namenode.http-bind-host

**yarn-site.xml 中的配置项**

- yarn.resourcemanager.bind-host
- yarn.nodemanager.bind-host

**mapred-site.xml 中的配置项**

- mapreduce.jobhistory.webapp.address
