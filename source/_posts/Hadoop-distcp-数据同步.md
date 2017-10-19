title: Hadoop distcp 数据同步
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-10-19 10:18:10
---


### distcp 介绍

distcp 是 hadoop 集群间数据同步的工具。基于 MapReduce 进行分布式、错误处理、恢复和报告。

一般 distcp 使用如下：

    hadoop distcp -bandwidth 10 -m 50 -overwrite -strategy dynamic webhdfs://hadoop219:50070/code hdfs://ycluster/data/data-platform/cluster219/code

<!-- more -->

### 参数说明

- -p[rbugpcaxt]：禁止：r（副本数量）；b（块大小）；u（用户）；g（组）；p（权限）；c（checksum类型）；a（ACL）；x（xAttr）；t（时间戳）。
- -m：同时拷贝的最大并发数。指定拷贝数据的 MapTask 数量。
- -overwrite：覆盖目标数据。如果 map 失败，并且没有指定 -i 忽略失败，则分片中的文件都会重新拷贝。
- -strategy{dynamic|uniformsize}：选择 distcp 使用的策略。默认使用 uniformsize（每个 map 会平衡文件大小）。如果指定 dynamic，则会使用 DynamicInputFormat。
- -bandwidth：指定每个 map 的带宽，单位 MB/s。

### 常见问题

#### 版本问题

不同版本间使用 hdfs 协议时可能会发生问题，如：

    $ hadoop fs -ls hdfs://hadoop219:50070/
    17/10/17 14:45:40 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
    ls: Failed on local exception: com.google.protobuf.InvalidProtocolBufferException: Protocol message end-group tag did not match expected tag.; Host Details : local host is: "192-168-72-200/192.168.72.200"; destination host is: "hadoop219":50070;
    
建议使用兼容性更好的 webhdfs 协议：

    $ hadoop fs -ls webhdfs://hadoop219:50070/
    17/10/17 14:45:29 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
    Found 9 items
    drwxr-xr-x   - root          supergroup          0 2017-05-24 10:16 webhdfs://hadoop219:50070/code
    drwxr-xr-x   - hadoop        supergroup          0 2017-07-25 12:07 webhdfs://hadoop219:50070/data
    drwxr-xr-x   - root          supergroup          0 2017-07-11 23:59 webhdfs://hadoop219:50070/data_log
    drwxr-xr-x   - Administrator supergroup          0 2015-07-27 20:40 webhdfs://hadoop219:50070/output
    drwxr-xr-x   - root          supergroup          0 2016-11-14 14:02 webhdfs://hadoop219:50070/recommend_v2
    drwxr-xr-x   - hadoop        supergroup          0 2016-07-06 08:29 webhdfs://hadoop219:50070/system
    drwx-wx-wx   - root          supergroup          0 2016-09-12 20:17 webhdfs://hadoop219:50070/tmp
    drwxr-xr-x   - root          supergroup          0 2016-07-21 17:09 webhdfs://hadoop219:50070/user
    drwxr-xr-x   - root          supergroup          0 2017-05-11 18:37 webhdfs://hadoop219:50070/yl_test

#### Check-sum mismatch

异常信息如下：

    Caused by: java.io.IOException: Check-sum mismatch between webhdfs://hadoop219:50070/code/bss_city_device/2017-07-31/part-m-00002 and hdfs://ycluster/data/data-platform/cluster219/code/.distcp.tmp.attempt_1506582668997_345304_m_000011_1. Source and target differ in block-size. Use -pb to preserve block-sizes during copy. Alternatively, skip checksum-checks altogether, using -skipCrc. (NOTE: By skipping checksums, one runs the risk of masking data-corruption during file-transfer.)
    	at org.apache.hadoop.tools.mapred.RetriableFileCopyCommand.compareCheckSums(RetriableFileCopyCommand.java:210)
    	at org.apache.hadoop.tools.mapred.RetriableFileCopyCommand.doCopy(RetriableFileCopyCommand.java:130)
    	at org.apache.hadoop.tools.mapred.RetriableFileCopyCommand.doExecute(RetriableFileCopyCommand.java:99)
    	at org.apache.hadoop.tools.util.RetriableCommand.execute(RetriableCommand.java:87)
    	... 11 more

根据异常信息可以看到是两个集群之间 block size 不一致引起的。增加 -pb 参数禁止块大小检查。
