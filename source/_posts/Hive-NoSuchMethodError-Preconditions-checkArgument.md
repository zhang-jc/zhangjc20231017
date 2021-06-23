title: Hive NoSuchMethodError Preconditions.checkArgument
date: 2021-06-23 17:49:50
tags:
- 大数据
- Hive
- Hadoop
categories:
- 大数据
- Hive
---

异常信息如下：

```SHELL
Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V
  at org.apache.hadoop.conf.Configuration.set(Configuration.java:1357)
  at org.apache.hadoop.conf.Configuration.set(Configuration.java:1338)
  at org.apache.hadoop.conf.Configuration.setBoolean(Configuration.java:1679)
  at org.apache.hadoop.hive.metastore.conf.MetastoreConf.setBoolVar(MetastoreConf.java:1379)
  at org.apache.hadoop.hive.metastore.conf.MetastoreConf.newMetastoreConf(MetastoreConf.java:1196)
  at org.apache.hadoop.hive.metastore.HiveMetaStore.main(HiveMetaStore.java:8770)
  at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
  at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
  at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
  at java.lang.reflect.Method.invoke(Method.java:498)
  at org.apache.hadoop.util.RunJar.run(RunJar.java:323)
  at org.apache.hadoop.util.RunJar.main(RunJar.java:236)
```

这个异常是Hive和Hadoop依赖的guava版本不一致造成的。解决方法是查看Hadoop安装目录下share/hadoop/common/lib和Hive安装目录下lib中guava的版本，删除版本低的，并拷贝高版本的。