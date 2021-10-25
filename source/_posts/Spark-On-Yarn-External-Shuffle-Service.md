---
title: Spark On Yarn External Shuffle Service
date: 2021-10-25 16:03:02
tags:
- 大数据
- Spark
- Hadoop
- Yarn
categories:
- 大数据
- Spark
---

本文基于spark-3.1.2-bin-without-hadoop.tgz版本，所以spark-3.1.2-yarn-shuffle.jar已经在节点的${SPARK_HOME}/yarn目录下。

# HADOOP_CLASSPATH

在所有NodeManager节点上设置环境变量：

```Shell
export HADOOP_CLASSPATH=${SPARK_HOME}/yarn/spark-3.1.2-yarn-shuffle.jar:$HADOOP_CLASSPATH
```

# yarn-site.xml

在yarn-site.xml中添加以下配置，并分发至所有NodeManager节点。

```XML
<!-- Spark Configuration -->
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>spark_shuffle</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services.spark_shuffle.class</name>
  <value>org.apache.spark.network.yarn.YarnShuffleService</value>
</property>
<property>
  <name>spark.yarn.shuffle.stopOnFailure</name>
  <value>true</value>
</property>
```

# 重启NodeManager

```Shell
yarn --daemon stop nodemanager
yarn --daemon start nodemanager
```