---
title: Hive On Spark 与 Spark On Hive
date: 2021-10-26 11:29:25
tags:
- 大数据
- Hive
- Spark
categories:
- 大数据
- Spark
---

两者的的共同点是底层计算引擎都是Spark。区别主要是SQL由谁编译。

- Hive On Spark：SQL由Hive编译，并提交给Spark运行。
- Spark On Hive：SQL由Spark编译，编译过程需要Hive的Metastore服务及情况包。

两种方式如何选择呢？先说结论，我选择的是Spark On Hive。原因是，根据两者各自官网的文档描述来看，Spark的开放性和兼容性更好。如下：

- Spark官网文档：[Hive Tables](http://spark.apache.org/docs/3.1.2/sql-data-sources-hive-tables.html)

从文档中可以看出Spark针对Hive不同版本的Metastore服务都有兼容。如下图：

![Interacting with Different Versions of Hive Metastore](/images/20211026/spark-hive-metastore-version.png)

- Hive官网文档：[Hive on Spark](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started)

从文档中可以看出Hive有保障支持的Spark最新版本为2.3.0。其他版本虽然也可以运行，但非官方保障的。如下图：

![Version Compatibility](/images/20211026/hive-spark-version.png)