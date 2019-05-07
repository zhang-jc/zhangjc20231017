title: 'MapReduce 读取 Hive ORC ArrayIndexOutOfBoundsException: 1024 异常解决'
date: 2019-05-07 10:26:47
tags:
- Hadoop
- Hive
- ORC
---

在 MR 处理 ORC 的时候遇到如下异常：

    Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 1024
    	at org.apache.orc.impl.RunLengthIntegerReaderV2.nextVector    (RunLengthIntegerReaderV2.java:369)
    	at org.apache.orc.impl.TreeReaderFactory$BytesColumnVectorUtil.commonReadByteArrays    (TreeReaderFactory.java:1231)
    	at org.apache.orc.impl.TreeReaderFactory$BytesColumnVectorUtil.readOrcByteArrays    (TreeReaderFactory.java:1268)
    	at org.apache.orc.impl.TreeReaderFactory$StringDirectTreeReader.nextVector    (TreeReaderFactory.java:1368)
    	at org.apache.orc.impl.TreeReaderFactory$StringTreeReader.nextVector    (TreeReaderFactory.java:1212)
    	at org.apache.orc.impl.TreeReaderFactory$ListTreeReader.nextVector    (TreeReaderFactory.java:1902)
    	at org.apache.orc.impl.TreeReaderFactory$StructTreeReader.nextBatch    (TreeReaderFactory.java:1737)
    	at org.apache.orc.impl.RecordReaderImpl.nextBatch(RecordReaderImpl.java:1045)
    	at org.apache.hadoop.hive.ql.io.orc.RecordReaderImpl.ensureBatch(RecordReaderImpl.java:77)
    	at org.apache.hadoop.hive.ql.io.orc.RecordReaderImpl.hasNext(RecordReaderImpl.java:89)

通过搜索发现这个 Bug 在 Hive 2.1.1 版本中已经修复。我使用的就是这个版本，检查对应的源代码发现代码是已经按照下面的 Patch 修复过得：https://issues.apache.org/jira/browse/HIVE-14483 

通过反编译发现我最终打包后的代码中使用的是未修复 Bug 的代码版本。通过依赖包发现依赖的以下模块中也包含 ORC 的 Jar：

    <dependency>
	    <groupId>org.apache.orc</groupId>
		<artifactId>orc-mapreduce</artifactId>
		<version>1.1.0</version>
	</dependency>

解决方法是将 orc-mapreduce 包升级到 1.1.2 版本，依赖配置如下：

    <dependency>
	    <groupId>org.apache.orc</groupId>
		<artifactId>orc-mapreduce</artifactId>
		<version>1.1.2</version>
	</dependency>