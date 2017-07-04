title: hdfs-audit.log Permission denied
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-07-04 22:31:44
---

今天在列出 HDFS 文件时遇到异常 java.io.FileNotFoundException: /data/hadoop/logs/hdfs-audit.log (Permission denied)，详细异常信息如下：

<!-- more -->

    $ hadoop fs -ls /
    log4j:ERROR setFile(null,true) call failed.
    java.io.FileNotFoundException: /data/hadoop/logs/hdfs-audit.log (Permission denied)
        at java.io.FileOutputStream.open0(Native Method)
        at java.io.FileOutputStream.open(FileOutputStream.java:270)
        at java.io.FileOutputStream.<init>(FileOutputStream.java:213)
        at java.io.FileOutputStream.<init>(FileOutputStream.java:133)
        at org.apache.log4j.FileAppender.setFile(FileAppender.java:294)
        at org.apache.log4j.RollingFileAppender.setFile(RollingFileAppender.java:207)
        at org.apache.log4j.FileAppender.activateOptions(FileAppender.java:165)
        at org.apache.log4j.config.PropertySetter.activate(PropertySetter.java:307)
        at org.apache.log4j.config.PropertySetter.setProperties(PropertySetter.java:172)
        at org.apache.log4j.config.PropertySetter.setProperties(PropertySetter.java:104)
        at org.apache.log4j.PropertyConfigurator.parseAppender(PropertyConfigurator.java:842)
        at org.apache.log4j.PropertyConfigurator.parseCategory(PropertyConfigurator.java:768)
        at org.apache.log4j.PropertyConfigurator.parseCatsAndRenderers(PropertyConfigurator.java:672)
        at org.apache.log4j.PropertyConfigurator.doConfigure(PropertyConfigurator.java:516)
        at org.apache.log4j.PropertyConfigurator.doConfigure(PropertyConfigurator.java:580)
        at org.apache.log4j.helpers.OptionConverter.selectAndConfigure(OptionConverter.java:526)
        at org.apache.log4j.LogManager.<clinit>(LogManager.java:127)
        at org.apache.log4j.Logger.getLogger(Logger.java:104)
        at org.apache.commons.logging.impl.Log4JLogger.getLogger(Log4JLogger.java:262)
        at org.apache.commons.logging.impl.Log4JLogger.<init>(Log4JLogger.java:108)
        at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
        at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
        at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
        at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
        at org.apache.commons.logging.impl.LogFactoryImpl.createLogFromClass(LogFactoryImpl.java:1025)
        at org.apache.commons.logging.impl.LogFactoryImpl.discoverLogImplementation(LogFactoryImpl.java:844)
        at org.apache.commons.logging.impl.LogFactoryImpl.newInstance(LogFactoryImpl.java:541)
        at org.apache.commons.logging.impl.LogFactoryImpl.getInstance(LogFactoryImpl.java:292)
        at org.apache.commons.logging.impl.LogFactoryImpl.getInstance(LogFactoryImpl.java:269)
        at org.apache.commons.logging.LogFactory.getLog(LogFactory.java:657)
        at org.apache.hadoop.fs.FsShell.<clinit>(FsShell.java:43)
    Found 2 items
    drwxr-xr-x   - hadoop   supergroup          0 2017-06-01 18:11 /data
    drwxrwxrwx   - hadoop   supergroup          0 2017-05-11 20:29 /tmp

根据错误信息很明显，是文件 /data/hadoop/logs/hdfs-audit.log 没有访问权限。因为 log4j.properties 中 hdfs audit 日志的配置是 hdfs.audit.logger=INFO,NullAppender，所以可以直接删除文件 /data/hadoop/logs/hdfs-audit.log。或者可以修改该文件的权限。
