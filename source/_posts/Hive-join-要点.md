title: Hive join 要点
tags:
  - Hive
categories:
  - 大数据
  - Hive
date: 2017-07-08 17:59:59
---

在写 Hive join 查询时需要关注以下要点：

- 允许复杂关联表达式：

    SELECT a.* FROM a JOIN b ON (a.id = b.id);
    SELECT a.* FROM a JOIN b ON (a.id = b.id AND a.department = b.department);
    SELECT a.* FROM a LEFT OUTER JOIN b ON (a.id <> b.id);

以上都是有效的关联。

<!-- more -->

- 在一个查询中可以关联 2 张以上的表：

    SELECT a.val, b.val, c.val FROM a JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key2);

是有效的关联。

- 如果在关联从句中所有表都使用同一列进行关联，Hive 会转化多表关联为一个 map / reduce 任务：

    SELECT a.val, b.val, c.val FROM a JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key1);

上面的语句会被转化为一个 map / reduce 任务，因为只有 b 的 key1 列被用作关联。换句话说，

    SELECT a.val, b.val, c.val FROM a JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key2);
    
上面的语句会被转化为两个 map / reduce 任务，因为第一个关联条件使用 b 中的 key1，b 中的 key2 用在第二个关联条件中。第一个 map / reduce 任务关联 a 和 b，然后结果在第二个 map / reduce 任务中与 c 关联。

- 在关联的每个 map / reduce 阶段，序列中最后的表通过 reducer 被分流，其他表被缓存在 reducer 中。因此，通过组织表，是最大的表出现在序列的最后，缓存关联键特定值的行在 reducer 中，有助于降低 reducer 内存的需求。例如，在下面的语句中：

    SELECT a.val, b.val, c.val FROM a JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key1);

三张表在一个 map / reduce 任务中关联，表 a 和 表 b 的关联键的特定值被缓存在 reducer 的内存中。然后对从 c 获取的每一行用缓存的每一行来计算关联。类似的：

    SELECT a.val, b.val, c.val FROM a JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key2);
    
计算这个关联需要调起两个 map / reduce 任务。第一个任务关联 a 和 b，缓存 a 的值并在 reducer 中分流 b 的值。第二个任务缓存第一个任务的结果，且通过 reducer 分流 c 的值。

- 在关联的每个 map / reduce 阶段，可以通过提示指定被分流的表。例如，在下面的语句中：

    SELECT /*+ STREAMTABLE(a) */ a.val, b.val, c.val FROM a JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key1);

三张表在一个 map / reduce 任务中关联，表 b 和 表 c 的关联键的特定值被缓存在 reducer 的内存中。然后，对从 a 获取的每一行用缓存的每一行来计算关联。如果 STREAMTABLE 提示被省略，Hive 分流关联中最右面的a表。

- LEFT、RIGHT 和 FULL OUTER 关联的存在是为了对 ON 字句不能匹配的记录提供更多的控制。例如：

    SELECT a.val, b.val FROM a LEFT OUTER JOIN b ON (a.key=b.key);

这个查询将返回 a 中所有的行。当 b.key 等于 a.key 时输出为“a.val,b.val”；当没有相应的 b.key 时输出为“a.val,NULL”。b 中没有对应 a.key 的行被丢弃。为了理解“FROM a LEFT OUTER JOIN b”语法是如何工作的必须写在一行——在这个查询中 a 是 b 的 LEFT，因此所有 a 中的行被保留；a RIGHT OUTER JOIN 会保留 b 中所有的行；a FULL OUTER JOIN 将会保留 a 中的所有行及 b 中所有的行。OUTER JOIN 应该符合标准的 SQL 规范。

- 关联发生在 WHERE 从句之前。因此，如果要限制关联的输出，条件应该在 WHERE 从句中，否则它应该在 JOIN 从句中。这个问题的一大困惑是分区表：

    SELECT a.val, b.val FROM a LEFT OUTER JOIN b ON (a.key=b.key)
    WHERE a.ds='2009-07-07' AND b.ds='2009-07-07';

这个查询会关联 a 和 b，生成一个 a.val 和  b.val 的列表。然而，WHERE 从句可以引用关联输出中 a 和 b 的其他列，然后过滤它们之后输出。然而无论何时，JOIN 中的一行发现 a 的一个 key 且没有 b 的 key，所有 b 的列将会是 NULL，包括 ds 列。这就是说，你将过滤出没有有效 b.key 的关联的所有行，因此你对 LEFT OUTER 有更明智的要求。换种说法，关联的 LEFT OUTER 部分跟 WHERE 从句中引用 b 中的任意列是不相关的。反而，当外关联时，使用这个语法：

    SELECT a.val, b.val FROM a LEFT OUTER JOIN b
    ON (a.key=b.key AND b.ds='2009-07-07' AND a.ds='2009-07-07');

结果是关联的输出被提前过滤，对于有效 a.key 但没有匹配的 b.key 的行避免了后过滤。同样的逻辑也适用于 RIGHT 和 FULL 关联。

**测试两种写法的输出是否有区别：**

数据准备：

    create table a(key string,ds string);
    create table b(key string,ds string);
    insert into a values('k1','2009-07-07'),('k2','2009-07-07'),('k3','2009-07-08'),('k4','2009-07-07'),('k6','2009-07-07');
    insert into b values('k1','2009-07-07'),('k2','2009-07-07'),('k3','2009-07-07'),('k4','2009-07-08'),('k5','2009-07-07');

第一种方法：

    SELECT a.key, b.key FROM a LEFT OUTER JOIN b ON (a.key=b.key) WHERE a.ds='2009-07-07' AND b.ds='2009-07-07';
    k1	k1
    k2	k2

第二种方法：

    SELECT a.key, b.key FROM a LEFT OUTER JOIN b ON (a.key=b.key AND b.ds='2009-07-07' AND a.ds='2009-07-07');
    k1	k1
    k2	k2
    k3	NULL
    k4	NULL
    k6	NULL

- 关联是不能交换的！关联是左结合的，不管是 LEFT 还是 RIGHT 关联。

    SELECT a.val1, a.val2, b.val, c.val
    FROM a
    JOIN b ON (a.key = b.key)
    LEFT OUTER JOIN c ON (a.key = c.key);

首先关联 a 和 b，丢弃掉 a 和 b 所有不能关联的行。之后的表与 c 关联。这为如果在 a 和 c 中都存在的 key 但是在 b 中不存在的情况提供了直观的结果：整行（包括 a.val1、a.val2 和 a.key）都会在“a JOIN b”环节丢弃掉，因为它不在 b 中。结果中没有 a.key，因此当与 c 进行 LEFT OUTER JOIN 时，c.val 也不在，因为没有 c.key 匹配 a.key（因此 a 中的行被移除）。同样的，如果是 RIGHT OUTER JOIN（而不是 LEFT），我们最终会得到意向不到的效果：NULL, NULL, NULL, c.val，因为即使我们指定了 a.key=c.key 作为关联键，也会在第一个关联中丢弃不匹配的 a 中的行。

为了得到更直观的效果，应该替换为 FROM c LEFT OUTER JOIN a ON (c.key = a.key) LEFT OUTER JOIN b ON (c.key = b.key)。

- LEFT SEMI JOIN 以一种有效的方式实现了无关联的 IN/EXISTS 子查询表达式。从 Hive 0.13 起 IN/NOT IN/EXISTS/NOT EXISTS 操作符通过使用子查询被支持，所以大多数这些关联不需要再手动执行。使用 LEFT SEMI JOIN 的限制是，右手边的表只能在关联条件（ON 字句）中被引用，不能在 WHERE 或者 SELECT 中使用。

    SELECT a.key, a.value
    FROM a
    WHERE a.key in
     (SELECT b.key
      FROM B);

可以被写作：

    SELECT a.key, a.val
    FROM a LEFT SEMI JOIN b ON (a.key = b.key);
    
- 如果除了一张表外其他表都很小，关联可以被优化为一个只有 map 的任务。

    SELECT /*+ MAPJOIN(b) */ a.key, a.value
    FROM a JOIN b ON a.key = b.key;

这个查询不需要 reducer。每一个 A 的 mapper，B 都被完全读入。限制是 a FULL/RIGHT OUTER JOIN b 不能被优化。

- 如果关联的表以关联的列被分桶化，并且一张表的桶数量是另外一张表桶数量的很多倍，那么每个桶可以互相关联。如果表 A 有 4 个桶，表 B 也有 4 个桶，下面的关联可以只在 mapper 中完成。

    SELECT /*+ MAPJOIN(b) */ a.key, a.value
    FROM a JOIN b ON a.key = b.key;

只获取需要的桶，而不是针对 A 的每个 mapper 都获取 B 的全部。对于上面的查询，处理 A 的桶 1 的 mapper 只获取 B 的桶 1。这不是默认行为，由以下参数控制：

    set hive.optimize.bucketmapjoin = true;
    
- 如果关联的表以关联的列排序且分桶化，并且它们有相同的桶数量，可以进行合并排序关联。在 mapper 中相关的桶彼此关联。如果 A 和 B 都有 4 个桶，下面的关联可以只在 mapper 中完成。

    SELECT /*+ MAPJOIN(b) */ a.key, a.value
    FROM A a JOIN B b ON a.key = b.key;

A 的桶的 mapper 将通过相关的 B 的桶处理。这不是默认行为，需要设置下面的参数：

    set hive.input.format=org.apache.hadoop.hive.ql.io.BucketizedHiveInputFormat;
    set hive.optimize.bucketmapjoin = true;
    set hive.optimize.bucketmapjoin.sortedmerge = true;
