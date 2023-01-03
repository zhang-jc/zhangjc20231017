---
title: Hive/Spark SQL数字格式化
date: 2022-11-15 11:24:54
tags:
- Hive
- Spark
- 大数据
---

如果数字长度过大，则会以科学技术法的方式展现。通过一下例子可以复现，如下：

```SQL
create table t1 (d decimal(18,8)) stored as orc;

insert into t1 values(0.00000001);

select * from t1;
```

如果需要按照原始数值展示，则可以用使用format_number函数，如下：

```SQL
select format_number(d, 8) from t1;
```

format_number的说明参见Hive官网：<https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-StringFunctions>。该函数在Spark SQL下也可使用。