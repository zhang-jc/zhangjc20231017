---
title: MapReduce远程调试
date: 2021-08-05 18:21:48
tags:
- Hadoop
- 大数据
- Eclipse
categories:
- 大数据
- Hadoop
---

MR的远程调试分为两个方面：

- MapReduce Task远程调试
- AM（Application Master）远程调试

# MapReduce Task远程调试
## Map Task远程调试

修改mapred-site.xml中的配置项：

- mapreduce.map.java.opts：Map Task JVM参数设置。此处设置为：-Xdebug -Xrunjdwp:transport=dt_socket,address=9091,server=y,suspend=y。
- mapreduce.map.maxattempts：Map Task失败重试的最大次数。此处设置为1。
- mapreduce.task.timeout：一个Task既没有读写数据也没有状态变化可持续的毫秒数。默认为600000毫秒，设置为0禁用超时。远程调试时通常需要持续的时间比较久，所以此处设置为0。

## Reduce Task远程调试

修改mapred-site.xml中的配置项：

- mapreduce.reduce.java.opts：Map Task JVM参数设置。此处设置为：-Xdebug -Xrunjdwp:transport=dt_socket,address=9091,server=y,suspend=y。
- mapreduce.reduce.maxattempts：Map Task失败重试的最大次数。此处设置为1。
- mapreduce.task.timeout：一个Task既没有读写数据也没有状态变化可持续的毫秒数。默认为600000毫秒，设置为0禁用超时。远程调试时通常需要持续的时间比较久，所以此处设置为0。

## virtual memory错误

分为两种：

- AM管理进程virtual memory beyond错误
- Task Container virtual memory beyond错误

### AM

错误信息如下：

```Log
21/08/09 16:38:37 INFO mapreduce.Job: Job job_1593052953461_29983110 failed with state FAILED due to: Application application_1593052953461_29983110 failed 2 times due to AM Container for appattempt_1593052953461_29983110_000002 exited with  exitCode: -103
For more detailed output, check application tracking page:http://ycluster-resourcemanager1:50030/cluster/app/application_1593052953461_29983110Then, click on links to logs of each attempt.
Diagnostics: Container [pid=4359,containerID=container_e32_1593052953461_29983110_02_000001] is running beyond virtual memory limits. Current usage: 121.7 MB of 2 GB physical memory used; 16.4 GB of 6.2 GB virtual memory used. Killing container.
Dump of the process-tree for container_e32_1593052953461_29983110_02_000001 :
	|- PID PPID PGRPID SESSID CMD_NAME USER_MODE_TIME(MILLIS) SYSTEM_TIME(MILLIS) VMEM_USAGE(BYTES) RSSMEM_USAGE(PAGES) FULL_CMD_LINE
	|- 4598 4359 4359 4359 (java) 232 18 17444765696 30784 /opt/java/bin/java -Djava.io.tmpdir=/data5/hadoop/yarn/data/usercache/data-platform/appcache/application_1593052953461_29983110/container_e32_1593052953461_29983110_02_000001/tmp -Dlog4j.configuration=container-log4j.properties -Dyarn.app.container.log.dir=/data1/hadoop/yarn/log/application_1593052953461_29983110/container_e32_1593052953461_29983110_02_000001 -Dyarn.app.container.log.filesize=0 -Dhadoop.root.logger=INFO,CLA -Dhadoop.root.logfile=syslog -Xmx14336m org.apache.hadoop.mapreduce.v2.app.MRAppMaster 
	|- 4359 4355 4359 4359 (bash) 3 3 115908608 359 /bin/bash -c /opt/java/bin/java -Djava.io.tmpdir=/data5/hadoop/yarn/data/usercache/data-platform/appcache/application_1593052953461_29983110/container_e32_1593052953461_29983110_02_000001/tmp -Dlog4j.configuration=container-log4j.properties -Dyarn.app.container.log.dir=/data1/hadoop/yarn/log/application_1593052953461_29983110/container_e32_1593052953461_29983110_02_000001 -Dyarn.app.container.log.filesize=0 -Dhadoop.root.logger=INFO,CLA -Dhadoop.root.logfile=syslog  -Xmx14336m org.apache.hadoop.mapreduce.v2.app.MRAppMaster 1>/data1/hadoop/yarn/log/application_1593052953461_29983110/container_e32_1593052953461_29983110_02_000001/stdout 2>/data1/hadoop/yarn/log/application_1593052953461_29983110/container_e32_1593052953461_29983110_02_000001/stderr  

Container killed on request. Exit code is 143
Container exited with a non-zero exit code 143
Failing this attempt. Failing the application.
```

修改yarn-site.xml以下配置：

```XML
<property>
  <name>yarn.app.mapreduce.am.resource.mb</name>
  <value>14336</value>
</property>
```

### Task Container

错误信息如下：

```Log
2021-08-09 16:45:28,899 INFO [AsyncDispatcher event handler] org.apache.hadoop.mapreduce.v2.app.job.impl.TaskAttemptImpl: Diagnostics report from attempt_1593052953461_29983142_m_000000_0: Container [pid=188730,containerID=container_e32_1593052953461_29983142_01_000002] is running beyond virtual memory limits. Current usage: 25.3 MB of 4 GB physical memory used; 32.9 GB of 12.4 GB virtual memory used. Killing container.
Dump of the process-tree for container_e32_1593052953461_29983142_01_000002 :
	|- PID PPID PGRPID SESSID CMD_NAME USER_MODE_TIME(MILLIS) SYSTEM_TIME(MILLIS) VMEM_USAGE(BYTES) RSSMEM_USAGE(PAGES) FULL_CMD_LINE
	|- 188965 188730 188730 188730 (java) 8 2 35216101376 6116 /opt/java/bin/java -Djava.net.preferIPv4Stack=true -Dhadoop.metrics.log.level=WARN -Xdebug -Xrunjdwp:transport=dt_socket,address=9091,server=y,suspend=y -Djava.io.tmpdir=/data0/hadoop/yarn/data/usercache/data-platform/appcache/application_1593052953461_29983142/container_e32_1593052953461_29983142_01_000002/tmp -Dlog4j.configuration=container-log4j.properties -Dyarn.app.container.log.dir=/data3/hadoop/yarn/log/application_1593052953461_29983142/container_e32_1593052953461_29983142_01_000002 -Dyarn.app.container.log.filesize=0 -Dhadoop.root.logger=INFO,CLA -Dhadoop.root.logfile=syslog org.apache.hadoop.mapred.YarnChild 192.168.72.33 32492 attempt_1593052953461_29983142_m_000000_0 35184372088834 
	|- 188730 188728 188730 188730 (bash) 2 4 115855360 358 /bin/bash -c /opt/java/bin/java -Djava.net.preferIPv4Stack=true -Dhadoop.metrics.log.level=WARN  -Xdebug -Xrunjdwp:transport=dt_socket,address=9091,server=y,suspend=y -Djava.io.tmpdir=/data0/hadoop/yarn/data/usercache/data-platform/appcache/application_1593052953461_29983142/container_e32_1593052953461_29983142_01_000002/tmp -Dlog4j.configuration=container-log4j.properties -Dyarn.app.container.log.dir=/data3/hadoop/yarn/log/application_1593052953461_29983142/container_e32_1593052953461_29983142_01_000002 -Dyarn.app.container.log.filesize=0 -Dhadoop.root.logger=INFO,CLA -Dhadoop.root.logfile=syslog org.apache.hadoop.mapred.YarnChild 192.168.72.33 32492 attempt_1593052953461_29983142_m_000000_0 35184372088834 1>/data3/hadoop/yarn/log/application_1593052953461_29983142/container_e32_1593052953461_29983142_01_000002/stdout 2>/data3/hadoop/yarn/log/application_1593052953461_29983142/container_e32_1593052953461_29983142_01_000002/stderr  

Container killed on request. Exit code is 143
Container exited with a non-zero exit code 143
```

修改yarn-site.xml以下配置：

```XML
<property>
  <name>mapreduce.map.memory.mb</name>
  <value>14336</value>
</property>
<property>
  <name>mapreduce.reduce.memory.mb</name>
  <value>14336</value>
</property>
```

> 此处有一个很大的疑问：为什么物理内存占用很小，但虚拟内存占用会这么大？这个问题目前还未搞懂(>﹏<)
> 问题解决的关键是Container分配物理内存的值乘以“yarn.nodemanager.vmem-pmem-ratio”的值大于异常信息中的数据；另外一种思路是禁用虚拟内存的检查，这种方法我没有实际验证。


## 定位

打开track url地址，查找到Task所在的服务器。如下：

![track url](/images/20210809/track-url.png)

![job info](/images/20210809/job-info.png)

![task position](/images/20210809/task-position.png)

# AM（Application Master）远程调试
## 配置

修改mapred-site.xml中的配置项：

- yarn.app.mapreduce.am.command-opts：AM JVM参数设置。可能出现的virtual memory异常解决方法同上。此处设置为：-Xdebug -Xrunjdwp:transport=dt_socket,address=9091,server=y,suspend=y。
- mapreduce.am.max-attempts：Application重试次数。此处设置为1。

## 定位

在ResourceManager上根据提交的Application ID查找AM所在的服务器。如下：

![ResourceManager](/images/20210809/yarn-apps.png)

![AM](/images/20210809/am.png)

# Eclipse配置

远程调试配置都一样，如下图：

![Eclipse远程调试](/images/20210809/eclipse.png)