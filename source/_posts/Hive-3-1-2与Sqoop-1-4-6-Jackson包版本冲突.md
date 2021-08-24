---
title: Hive 3.1.2与Sqoop 1.4.6 Jackson包版本冲突
date: 2021-08-24 00:26:20
tags:
- Hive
- Sqoop
- 大数据
categories:
- 大数据
- Hive
---

异常信息如下：

```Log
Logging initialized using configuration in jar:file:/opt/apache-hive-3.1.2-bin/lib/hive-common-3.1.2.jar!/hive-log4j2.properties Async: true
Hive Session ID = 6d309a0e-02c3-4ef7-b8a7-7d42415fcd10
Exception in thread "main" java.lang.NoSuchMethodError: com.fasterxml.jackson.databind.ObjectMapper.readerFor(Ljava/lang/Class;)Lcom/fasterxml/jackson/databind/ObjectReader;
	at org.apache.hadoop.hive.common.StatsSetupConst$ColumnStatsAccurate.<clinit>(StatsSetupConst.java:166)
	at org.apache.hadoop.hive.common.StatsSetupConst.parseStatsAcc(StatsSetupConst.java:314)
	at org.apache.hadoop.hive.common.StatsSetupConst.areBasicStatsUptoDate(StatsSetupConst.java:206)
	at org.apache.hadoop.hive.ql.stats.StatsUtils.areBasicStatsUptoDateForQueryAnswering(StatsUtils.java:2010)
	at org.apache.hadoop.hive.ql.optimizer.StatsOptimizer$MetaDataProcessor.getRowCnt(StatsOptimizer.java:915)
	at org.apache.hadoop.hive.ql.optimizer.StatsOptimizer$MetaDataProcessor.process(StatsOptimizer.java:299)
	at org.apache.hadoop.hive.ql.lib.DefaultRuleDispatcher.dispatch(DefaultRuleDispatcher.java:90)
	at org.apache.hadoop.hive.ql.lib.DefaultGraphWalker.dispatchAndReturn(DefaultGraphWalker.java:105)
	at org.apache.hadoop.hive.ql.lib.DefaultGraphWalker.dispatch(DefaultGraphWalker.java:89)
	at org.apache.hadoop.hive.ql.lib.DefaultGraphWalker.walk(DefaultGraphWalker.java:158)
	at org.apache.hadoop.hive.ql.lib.DefaultGraphWalker.startWalking(DefaultGraphWalker.java:120)
	at org.apache.hadoop.hive.ql.optimizer.StatsOptimizer.transform(StatsOptimizer.java:134)
	at org.apache.hadoop.hive.ql.optimizer.Optimizer.optimize(Optimizer.java:250)
	at org.apache.hadoop.hive.ql.parse.SemanticAnalyzer.analyzeInternal(SemanticAnalyzer.java:12295)
	at org.apache.hadoop.hive.ql.parse.CalcitePlanner.analyzeInternal(CalcitePlanner.java:330)
	at org.apache.hadoop.hive.ql.parse.BaseSemanticAnalyzer.analyze(BaseSemanticAnalyzer.java:285)
	at org.apache.hadoop.hive.ql.Driver.compile(Driver.java:659)
	at org.apache.hadoop.hive.ql.Driver.compileInternal(Driver.java:1826)
	at org.apache.hadoop.hive.ql.Driver.compileAndRespond(Driver.java:1773)
	at org.apache.hadoop.hive.ql.Driver.compileAndRespond(Driver.java:1768)
	at org.apache.hadoop.hive.ql.reexec.ReExecDriver.compileAndRespond(ReExecDriver.java:126)
	at org.apache.hadoop.hive.ql.reexec.ReExecDriver.run(ReExecDriver.java:214)
	at org.apache.hadoop.hive.cli.CliDriver.processLocalCmd(CliDriver.java:239)
	at org.apache.hadoop.hive.cli.CliDriver.processCmd(CliDriver.java:188)
	at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:402)
	at org.apache.hadoop.hive.cli.CliDriver.processLine(CliDriver.java:335)
	at org.apache.hadoop.hive.cli.CliDriver.executeDriver(CliDriver.java:787)
	at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:759)
	at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:683)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.hadoop.util.RunJar.run(RunJar.java:323)
	at org.apache.hadoop.util.RunJar.main(RunJar.java:236)
```

解决方法：删除${SQOOP_HOME}/lib下的jackson开头的Jar包。因为这些Jar包版本较低。