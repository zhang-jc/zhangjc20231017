---
title: 查看JVM默认垃圾回收器的方法
date: 2021-08-27 16:02:18
tags:
- Java
- JVM
categories:
- 语言
- Java
---

在JVM启动未指定垃圾回收器的情况下，使用以下命令查看默认的垃圾回收器：

```Shell
# java -XX:+PrintCommandLineFlags -version
-XX:InitialHeapSize=251658240 -XX:MaxHeapSize=4026531840 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseParallelGC
java version "1.8.0_281"
Java(TM) SE Runtime Environment (build 1.8.0_281-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.281-b09, mixed mode)
```

以上信息中-XX:+UseParallelGC表明用的是并行垃圾收集器。