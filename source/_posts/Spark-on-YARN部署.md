---
title: Spark on YARN部署
date: 2021-10-25 11:55:41
tags:
- 大数据
- Spark
- Hadoop
- Yarn
---

目前各大数据组件部署其实都比较简单，几乎接近开箱即用。此处只记录部署时遇到的问题。

# 问题一：java.lang.ClassNotFoundException: org.apache.log4j.spi.Filter

异常信息如下：

```Log
Error: A JNI error has occurred, please check your installation and try again
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/log4j/spi/Filter
	at java.lang.Class.getDeclaredMethods0(Native Method)
	at java.lang.Class.privateGetDeclaredMethods(Class.java:2701)
	at java.lang.Class.privateGetMethodRecursive(Class.java:3048)
	at java.lang.Class.getMethod0(Class.java:3018)
	at java.lang.Class.getMethod(Class.java:1784)
	at sun.launcher.LauncherHelper.validateMainClass(LauncherHelper.java:544)
	at sun.launcher.LauncherHelper.checkAndLoadMain(LauncherHelper.java:526)
Caused by: java.lang.ClassNotFoundException: org.apache.log4j.spi.Filter
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:349)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 7 more
```

从Spark3.1.2源码pom.xml中可以看到log4j的版本为：

```XML
<log4j.version>1.2.17</log4j.version>
```

下载对应版本的Jar包，并分发到各Worker节点的${SPARK_HOME}/jars下。

# 问题二：java.lang.ClassNotFoundException: org.apache.hadoop.fs.FSDataInputStream

异常信息如下：

```Log
Error: A JNI error has occurred, please check your installation and try again
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/hadoop/fs/FSDataInputStream
	at java.lang.Class.getDeclaredMethods0(Native Method)
	at java.lang.Class.privateGetDeclaredMethods(Class.java:2701)
	at java.lang.Class.privateGetMethodRecursive(Class.java:3048)
	at java.lang.Class.getMethod0(Class.java:3018)
	at java.lang.Class.getMethod(Class.java:1784)
	at sun.launcher.LauncherHelper.validateMainClass(LauncherHelper.java:544)
	at sun.launcher.LauncherHelper.checkAndLoadMain(LauncherHelper.java:526)
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.fs.FSDataInputStream
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:349)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	... 7 more
```

使用hadoop free版本部署遇到该问题。需要在spark-env.sh中设置包含hadoop的classpath。

```Shell
export SPARK_DIST_CLASSPATH=$(hadoop classpath)
```

> 官方文档有明确说明，只不过初次阅读容易忽略。位置如下：
> ![Spark Env](/images/20211025/hadoop-free-spark-env.png)