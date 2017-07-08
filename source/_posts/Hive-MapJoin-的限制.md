title: Hive MapJoin 的限制
tags:
  - 大数据
  - Hive
categories:
  - 大数据
  - Hive
date: 2017-07-08 20:56:22
---

- 如果除了一张表之外其他关联的表都很小，关联可以优化为只有 map 的作业。下面的查询不需要 reducer：

    SELECT /*+ MAPJOIN(b) */ a.key, a.value
    FROM a JOIN b ON a.key = b.key;

对于 A 的每个 mapper，都会完全读取 B。

<!-- more -->

- 下面是不支持的项：
  - Union 之后跟着 MapJoin
  - Lateral View 之后跟着 MapJoin
  - Reduce Sink（Group By/Join/Sort By/Cluster By/Distribute By）之后跟着 MapJoin
  - MapJoin 之后跟着 Union
  - MapJoin 之后跟着 Join
  - MapJoin 之后跟着 MapJoin

- 配置变量 hive.auto.convert.join 如果设置为 true，则如果可能在运行时会自动转化关联为 mapjoin，这用来代替 mapjoin 提示。mapjoin 提示应该只在以下查询中使用。
  - 如果输入都被分桶或者排序，那么关联应该转化为分桶化的 map 侧关联或者分桶化的合并排序关联。

- 考虑不同键的多个 mapjoin 的可能性：

    select /*+MAPJOIN(smallTableTwo)*/ idOne, idTwo, value FROM
      ( select /*+MAPJOIN(smallTableOne)*/ idOne, idTwo, value FROM
        bigTable JOIN smallTableOne on (bigTable.idOne = smallTableOne.idOne)                                                  
      ) firstjoin                                                            
      JOIN                                                                 
      smallTableTwo ON (firstjoin.idTwo = smallTableTwo.idTwo);

上面的查询不支持。去掉 mapjoin 提示，上面的查询会作为 2 个只有 map 的作业执行。如果用户知道输入足够小足以放入内存，下面的可配置参数可以用来确保查询在单个 map-reduce 作业中执行。

  - hive.auto.convert.join.noconditionaltask——是否启用 Hive 的基于输入文件大小的转化普通关联为 mapjoin 的优化。如果启用这个参数，并且 n 个 tables/partitions 关联的 n-1 个 tables/partitions 大小之和比指定的参数小，这个关联会被直接转化为 mapjoin（没有条件任务）。
  - hive.auto.convert.join.noconditionaltask.size——如果 hive.auto.convert.join.noconditionaltask 是关闭的，这个参数不起作用。然而，如果上面的参数开启，并且 n 个 tables/partitions 关联的 n-1 个 tables/partitions 大小之和比这个配置的参数小，那么这个关联被直接转化为 mapjoin（没有条件任务）。默认为 10MB。
