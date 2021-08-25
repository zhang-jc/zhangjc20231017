---
title: >-
  java.lang.AssertionError: Output column number expected to be 0 when
  isRepeating
date: 2021-08-25 16:03:45
tags:
- 大数据
- Hive
- Tez
categories:
- 大数据
- Hive
---

完整异常信息如下：

```Log
2021-08-25 14:37:24,329 [INFO] [Dispatcher thread {Central}] |HistoryEventHandler.criticalEvents|: [HISTORY][DAG:dag_1612602874723_344108_1][Event:TASK_ATTEMPT_FINISHED]: vertexName=Map 1, taskAttemptId=attempt_1612602874723_344108_1_00_000004_0, creationTime=1629873439340, allocationTime=1629873440827, startTime=1629873442059, finishTime=1629873444322, timeTaken=2263, status=FAILED, taskFailureType=FATAL, errorEnum=APPLICATION_ERROR, diagnostics=Error: Error while running task ( failure ) : java.lang.RuntimeException: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:101)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.pushRecord(MapRecordSource.java:76)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordProcessor.run(MapRecordProcessor.java:419)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:267)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:250)
	at org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:381)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:82)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:69)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:69)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:39)
	at org.apache.tez.common.CallableWithNdc.call(CallableWithNdc.java:36)
	at com.google.common.util.concurrent.TrustedListenableFutureTask$TrustedFutureInterruptibleTask.runInterruptibly(TrustedListenableFutureTask.java:125)
	at com.google.common.util.concurrent.InterruptibleTask.run(InterruptibleTask.java:57)
	at com.google.common.util.concurrent.TrustedListenableFutureTask.run(TrustedListenableFutureTask.java:78)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Caused by: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.vector.BytesColumnVector.setElement(BytesColumnVector.java:492)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFMapIndexBaseScalar.evaluate(VectorUDFMapIndexBaseScalar.java:84)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorCoalesce.evaluate(VectorCoalesce.java:60)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.gen.FilterStringGroupColNotEqualStringGroupScalarBase.evaluate(FilterStringGroupColNotEqualStringGroupScalarBase.java:64)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.FilterExprAndExpr.evaluate(FilterExprAndExpr.java:42)
	at org.apache.hadoop.hive.ql.exec.vector.VectorFilterOperator.process(VectorFilterOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.Operator.vectorForward(Operator.java:966)
	at org.apache.hadoop.hive.ql.exec.Operator.forward(Operator.java:939)
	at org.apache.hadoop.hive.ql.exec.TableScanOperator.process(TableScanOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.deliverVectorizedRowBatch(VectorMapOperator.java:812)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.process(VectorMapOperator.java:845)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:92)
	... 19 more
, errorMessage=Cannot recover from this error:java.lang.RuntimeException: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:101)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.pushRecord(MapRecordSource.java:76)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordProcessor.run(MapRecordProcessor.java:419)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:267)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:250)
	at org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:381)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:82)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:69)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:69)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:39)
	at org.apache.tez.common.CallableWithNdc.call(CallableWithNdc.java:36)
	at com.google.common.util.concurrent.TrustedListenableFutureTask$TrustedFutureInterruptibleTask.runInterruptibly(TrustedListenableFutureTask.java:125)
	at com.google.common.util.concurrent.InterruptibleTask.run(InterruptibleTask.java:57)
	at com.google.common.util.concurrent.TrustedListenableFutureTask.run(TrustedListenableFutureTask.java:78)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Caused by: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.vector.BytesColumnVector.setElement(BytesColumnVector.java:492)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFMapIndexBaseScalar.evaluate(VectorUDFMapIndexBaseScalar.java:84)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorCoalesce.evaluate(VectorCoalesce.java:60)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.gen.FilterStringGroupColNotEqualStringGroupScalarBase.evaluate(FilterStringGroupColNotEqualStringGroupScalarBase.java:64)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.FilterExprAndExpr.evaluate(FilterExprAndExpr.java:42)
	at org.apache.hadoop.hive.ql.exec.vector.VectorFilterOperator.process(VectorFilterOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.Operator.vectorForward(Operator.java:966)
	at org.apache.hadoop.hive.ql.exec.Operator.forward(Operator.java:939)
	at org.apache.hadoop.hive.ql.exec.TableScanOperator.process(TableScanOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.deliverVectorizedRowBatch(VectorMapOperator.java:812)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.process(VectorMapOperator.java:845)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:92)
	... 19 more
, nodeHttpAddress=datanode143-ysten3:8042, counters=Counters: 15, File System Counters, HDFS_BYTES_READ=216088, HDFS_BYTES_WRITTEN=3, HDFS_READ_OPS=3, HDFS_LARGE_READ_OPS=0, HDFS_WRITE_OPS=1, org.apache.tez.common.counters.TaskCounter, GC_TIME_MILLIS=136, CPU_MILLISECONDS=7620, WALL_CLOCK_MILLISECONDS=2160, PHYSICAL_MEMORY_BYTES=1120403456, VIRTUAL_MEMORY_BYTES=3798720512, COMMITTED_HEAP_BYTES=1120403456, INPUT_RECORDS_PROCESSED=26, INPUT_SPLIT_LENGTH_BYTES=10128027, OUTPUT_RECORDS=0, HIVE, CREATED_FILES=1
2021-08-25 14:37:24,332 [INFO] [Dispatcher thread {Central}] |impl.TaskImpl|: Failing task: task_1612602874723_344108_1_00_000004 due to FATAL error reported by TaskAttempt. CurrentFailedAttempts=1
2021-08-25 14:37:24,334 [INFO] [Dispatcher thread {Central}] |HistoryEventHandler.criticalEvents|: [HISTORY][DAG:dag_1612602874723_344108_1][Event:TASK_FINISHED]: vertexName=Map 1, taskId=task_1612602874723_344108_1_00_000004, startTime=1629873442059, finishTime=1629873444333, timeTaken=2274, status=FAILED, successfulAttemptID=null, diagnostics=TaskAttempt 0 failed, info=[Error: Error while running task ( failure ) : java.lang.RuntimeException: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:101)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.pushRecord(MapRecordSource.java:76)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordProcessor.run(MapRecordProcessor.java:419)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:267)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:250)
	at org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:381)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:82)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:69)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:69)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:39)
	at org.apache.tez.common.CallableWithNdc.call(CallableWithNdc.java:36)
	at com.google.common.util.concurrent.TrustedListenableFutureTask$TrustedFutureInterruptibleTask.runInterruptibly(TrustedListenableFutureTask.java:125)
	at com.google.common.util.concurrent.InterruptibleTask.run(InterruptibleTask.java:57)
	at com.google.common.util.concurrent.TrustedListenableFutureTask.run(TrustedListenableFutureTask.java:78)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Caused by: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.vector.BytesColumnVector.setElement(BytesColumnVector.java:492)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFMapIndexBaseScalar.evaluate(VectorUDFMapIndexBaseScalar.java:84)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorCoalesce.evaluate(VectorCoalesce.java:60)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.gen.FilterStringGroupColNotEqualStringGroupScalarBase.evaluate(FilterStringGroupColNotEqualStringGroupScalarBase.java:64)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.FilterExprAndExpr.evaluate(FilterExprAndExpr.java:42)
	at org.apache.hadoop.hive.ql.exec.vector.VectorFilterOperator.process(VectorFilterOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.Operator.vectorForward(Operator.java:966)
	at org.apache.hadoop.hive.ql.exec.Operator.forward(Operator.java:939)
	at org.apache.hadoop.hive.ql.exec.TableScanOperator.process(TableScanOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.deliverVectorizedRowBatch(VectorMapOperator.java:812)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.process(VectorMapOperator.java:845)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:92)
	... 19 more
, errorMessage=Cannot recover from this error:java.lang.RuntimeException: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:101)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.pushRecord(MapRecordSource.java:76)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordProcessor.run(MapRecordProcessor.java:419)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:267)
	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:250)
	at org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:381)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:82)
	at org.apache.tez.runtime.task.TaskRunner2Callable$1.run(TaskRunner2Callable.java:69)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.Subject.doAs(Subject.java:422)
	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1762)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:69)
	at org.apache.tez.runtime.task.TaskRunner2Callable.callInternal(TaskRunner2Callable.java:39)
	at org.apache.tez.common.CallableWithNdc.call(CallableWithNdc.java:36)
	at com.google.common.util.concurrent.TrustedListenableFutureTask$TrustedFutureInterruptibleTask.runInterruptibly(TrustedListenableFutureTask.java:125)
	at com.google.common.util.concurrent.InterruptibleTask.run(InterruptibleTask.java:57)
	at com.google.common.util.concurrent.TrustedListenableFutureTask.run(TrustedListenableFutureTask.java:78)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Caused by: java.lang.AssertionError: Output column number expected to be 0 when isRepeating
	at org.apache.hadoop.hive.ql.exec.vector.BytesColumnVector.setElement(BytesColumnVector.java:492)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorUDFMapIndexBaseScalar.evaluate(VectorUDFMapIndexBaseScalar.java:84)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorCoalesce.evaluate(VectorCoalesce.java:60)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.VectorExpression.evaluateChildren(VectorExpression.java:271)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.gen.FilterStringGroupColNotEqualStringGroupScalarBase.evaluate(FilterStringGroupColNotEqualStringGroupScalarBase.java:64)
	at org.apache.hadoop.hive.ql.exec.vector.expressions.FilterExprAndExpr.evaluate(FilterExprAndExpr.java:42)
	at org.apache.hadoop.hive.ql.exec.vector.VectorFilterOperator.process(VectorFilterOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.Operator.vectorForward(Operator.java:966)
	at org.apache.hadoop.hive.ql.exec.Operator.forward(Operator.java:939)
	at org.apache.hadoop.hive.ql.exec.TableScanOperator.process(TableScanOperator.java:125)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.deliverVectorizedRowBatch(VectorMapOperator.java:812)
	at org.apache.hadoop.hive.ql.exec.vector.VectorMapOperator.process(VectorMapOperator.java:845)
	at org.apache.hadoop.hive.ql.exec.tez.MapRecordSource.processRow(MapRecordSource.java:92)
	... 19 more
], counters=Counters: 0
```

解决方法是在hive-site.xml中添加如下配置：

```XML
  <property>
    <name>hive.vectorized.execution.enabled</name>
    <value>false</value>
  </property>
```