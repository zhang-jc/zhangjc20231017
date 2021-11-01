---
title: Spark SQL读写 Hive Table 部署
date: 2021-10-26 14:41:18
tags:
- 大数据
- Spark
- Hive
categories:
- 大数据
- Spark
---

官网参考文档：[Hive Tables](http://spark.apache.org/docs/3.1.2/sql-data-sources-hive-tables.html)。

本文使用的组件版本如下：

- Spark 3.1.2
- Hive 3.1.2

# 第一步：Hive部署

在所有Worker节点上部署Hive。主要是使用Hive下面的libs。

# 第二步：Spark CLASSPATH

在${SPARK_HOME}/conf/spark-env.sh中添加以下内容：

```Shell
export SPARK_DIST_CLASSPATH=$(hadoop classpath)
```

# 第三步：编译Spark

编译的目的是支持Hive。

```Shell
./dev/make-distribution.sh --tgz -Phive -Phive-thriftserver -Pyarn -Dhadoop.version=3.2.2 -Phadoop-provided
```

# 第四步：部署Spark On Yarn

部署过程比较简单。部署过程遇到的问题参见另外一篇博文：[Spark on YARN部署](https://www.zhangjc.com/2021/10/25/Spark-on-YARN%E9%83%A8%E7%BD%B2/)

# 第五步：兼容Hive 3.1.2

- 将hive-site.xml复制到spark配置目录下。
- 在配置文件spark-defaults.conf中添加以下内容。假设Hive安装目录为：/opt/hive。

```Conf
spark.sql.hive.metastore.version 3.1.2
spark.sql.hive.metastore.jars path
spark.sql.hive.metastore.jars.path file:///opt/hive/lib/*.jar
```

> 注意：这些Jar包需要使用Hive 3.1.2版本的。