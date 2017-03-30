title: >-
  REDUCE capability required is more than the supported max container capability
  in the cluster
tags:
  - Hadoop
  - Yarn
categories:
  - 大数据
  - Hadoop
date: 2017-03-03 10:56:11
---

今天在运行 MR 程序时遇到以下信息：

    17/03/03 10:09:45 INFO mapreduce.Job: Job job_1488363995041_0002 failed with state KILLED due to: REDUCE capability required is more than the supported max container capability in the cluster. Killing the Job. reduceResourceRequest: <memory:4096, vCores:1> maxContainerCapability:<memory:3096, vCores:8>

<!-- more -->

原因是 Reduce Task 在申请资源时超过了设置的最大可申请内容量。检查 yarn-site.xml 中的配置项 yarn.scheduler.maximum-allocation-mb，如下：

    <property>
      <name>yarn.scheduler.maximum-allocation-mb</name>
      <value>3096</value> 
    </property>

修改配置为 8192（默认配置也是 8192），分发配置，并重启 ResourceManager。再次运行 MR 成功。

**yarn.scheduler.minimum-allocation-mb / yarn.scheduler.maximum-allocation-mb**

参数解释：单个可申请的最小/最大内存资源量。比如设置为1024和3072，则运行MapRedce作业时，每个Task最少可申请1024MB内存，最多可申请3072MB内存。

默认值：1024 / 8192