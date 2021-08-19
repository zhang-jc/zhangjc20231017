---
title: Hive On Tez解决Guava版本冲突问题
date: 2021-08-19 16:54:25
tags:
- 大数据
- Hive
- Tez
categories:
- 大数据
- Hive
---

两种版本冲突场景。错误信息如下：

```Log
2021-08-19 15:58:42,885 [ERROR] [main] |app.DAGAppMaster|: Error starting DAGAppMaster
java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V
  at org.apache.hadoop.conf.Configuration.set(Configuration.java:1357)
  at org.apache.hadoop.conf.Configuration.set(Configuration.java:1338)
  at org.apache.tez.common.TezUtilsInternal.addUserSpecifiedTezConfiguration(TezUtilsInternal.java:84)
  at org.apache.tez.dag.app.DAGAppMaster.main(DAGAppMaster.java:2377)
```

# 环境信息

- Hadoop 3.2.2
- Hive 3.1.2
- Tez 0.10.1

# 场景一

${HIVE_HOME}/lib下的guava包与Hadoop的版本不一致。

对比${HIVE_HOME}/lib和${HADOOP_HOME}/share/hadoop/common/lib下的guava包版本，用高版本的替换低版本的即可。

# 场景二

hive-exec模块在打包的时候会包含guava包，依赖hive-exec的地方就会引发版本冲突。修改hive-exec模块的pom.xml文件，注释以下内容，然后重新编译打包hive即可。

```XML
<include>com.google.guava:guava</include>
```