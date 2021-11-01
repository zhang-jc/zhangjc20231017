---
title: Capacity Scheduler Resource Calculator
date: 2021-11-01 17:41:19
tags:
- Yarn
- Hadoop
- 大数据
- Spark
categories:
- 大数据
- Hadoop
---

在spark-sql提交查询时，指定了executor的cores数量，但Yarn显示每个container的cores数量一直为1。原因Capacity Scheduler默认只统计内存的使用。参考链接：[Capacity Scheduler](https://hadoop.apache.org/docs/r3.2.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)。

在yarn-site.xml配置文件中添加以下配置：

```XML
<property>
  <name>yarn.scheduler.capacity.resource-calculator</name>
  <value>org.apache.hadoop.yarn.util.resource.DominantResourceCalculator</value>
</property>
```

重启Yarn。