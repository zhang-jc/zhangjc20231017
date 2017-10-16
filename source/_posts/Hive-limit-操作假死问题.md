title: Hive limit 操作假死问题
tags:
  - Hive
categories:
  - 大数据
  - Hive
date: 2017-09-29 15:04:22
---


昨天同事反映 hive 在做 limit 查询时很慢，HQL 如下：

    select * from dwd.dwd_flow_box_playqos_day where curdate = '20170926' and final_play_time is null limit 10;
    
执行测试发现执行该 HQL 时，hive 客户端一直处于假死状态，没有任何信息输出，等待很久之后才有结果输出。

<!-- more -->

首先检查是否 MR 执行过程有问题。在 ResourceManager 上发现并没有对应的 MR Job 信息。难道这个 HQL 没有生成 MR？使用 explain 查看该 HQL 的执行计划，如下：

    hive> explain select * from dwd.dwd_flow_box_playqos_day where curdate = '20170926' and final_play_time is null limit 10;
    OK
    STAGE DEPENDENCIES:
      Stage-0 is a root stage
    
    STAGE PLANS:
      Stage: Stage-0
        Fetch Operator
          limit: 10
          Processor Tree:
            TableScan
              alias: dwd_flow_box_playqos_day
              Statistics: Num rows: 296929317 Data size: 1022092601961 Basic stats: COMPLETE Column stats: NONE
              Filter Operator
                predicate: final_play_time is null (type: boolean)
                Statistics: Num rows: 148464658 Data size: 511046299259 Basic stats: COMPLETE Column stats: NONE
                Select Operator
                  expressions: device_id (type: string), province_id (type: string), province_name (type: string), city_id (type: string), city_name (type: string), sp_type (type: string), terminal_id (type: string), state (type: string), activate_date (type: string), play_type (type: string), version_id (type: string), version_type (type: string), platform_id (type: string), mac (type: string), ip (type: string), req_time (type: string), cur_time (type: string), client_time (type: string), server_time (type: string), sort_id (type: string), url (type: string), opentimecost (type: string), buffcount (type: string), buffavertimecost (type: string), seekcount (type: string), seektimeavertimecost (type: string), playtotaltimecost (type: string), program_serise_id (type: string), program_series_name (type: string), program_series_type (type: string), uuid (type: string), playpoint (type: string), starttime (type: string), endtime (type: string), pausecount (type: string), pausetotaltime (type: string), null (type: string), is_short_video (type: string), '20170926' (type: string), terminal_type (type: string)
                  outputColumnNames: _col0, _col1, _col2, _col3, _col4, _col5, _col6, _col7, _col8, _col9, _col10, _col11, _col12, _col13, _col14, _col15, _col16, _col17, _col18, _col19, _col20, _col21, _col22, _col23, _col24, _col25, _col26, _col27, _col28, _col29, _col30, _col31, _col32, _col33, _col34, _col35, _col36, _col37, _col38, _col39
                  Statistics: Num rows: 148464658 Data size: 511046299259 Basic stats: COMPLETE Column stats: NONE
                  Limit
                    Number of rows: 10
                    Statistics: Num rows: 10 Data size: 34420 Basic stats: COMPLETE Column stats: NONE
                    ListSink
    
    Time taken: 2.003 seconds, Fetched: 23 row(s)
    
通过执行计划可以看出，该 HQL 确实没有 MR 过程，只有一个 Fetch Operator 过程。查看 Hive 文档发现，Hive 会将一些 HQL 转换单个 FETCH 任务，以降低延迟。是否允许转换通过 hive.fetch.task.conversion 进行配置：

    **hive.fetch.task.conversion**

    - Default Value: minimal in Hive 0.10.0 through 0.13.1, more in Hive 0.14.0 and later
    - Added In: Hive 0.10.0 with HIVE-2925; default changed in Hive 0.14.0 with HIVE-7397

    Some select queries can be converted to a single FETCH task, minimizing latency. Currently the query should be single sourced not having any subquery and should not have any aggregations or distincts (which incur RS – ReduceSinkOperator, requiring a MapReduce task), lateral views and joins.

    Supported values are none, minimal and more.

    - none:  Disable hive.fetch.task.conversion (value added in Hive 0.14.0 with HIVE-8389)
    - minimal:  SELECT *, FILTER on partition columns (WHERE and HAVING clauses), LIMIT only
    - more:  SELECT, FILTER, LIMIT only (including TABLESAMPLE, virtual columns)
             "more" can take any kind of expressions in the SELECT clause, including UDFs.
             (UDTFs and lateral views are not yet supported – see HIVE-5718.)

关闭该功能确实可以解决上面 HQL 出现的问题。但是，其他可以快速返回的 limit 查询就会被转为 MR 执行，返回速度会变慢。

Hive 配置中有一个 hive.fetch.task.conversion.threshold，该参数的作用是根据查询输入数据量判断是否将查询转换为单个 FETCH 任务。该参数默认配置为 1G。<span style="color:red;">但是，经过测试，发现在我的 Hive 2.1.1 上不起作用。这个问题需要继续定位。</span>

hive.fetch.task.conversion.threshold 参数说明如下：

    - Default Value: -1 in Hive 0.13.0 and 0.13.1, 1073741824 (1 GB) in Hive 0.14.0 and later 
    - Added In: Hive 0.13.0 with HIVE-3990; default changed in Hive 0.14.0 with HIVE-7397

    Input threshold (in bytes) for applying hive.fetch.task.conversion. If target table is native, input length is calculated by summation of file lengths. If it's not native, the storage handler for the table can optionally implement the org.apache.hadoop.hive.ql.metadata.InputEstimator interface. A negative threshold means hive.fetch.task.conversion is applied without any input length threshold.
