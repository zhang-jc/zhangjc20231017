title: Hive 加载文件数据到表
tags:
  - Hive
  - SQL
  - LOAD
categories:
  - 大数据
  - Hive
date: 2016-05-15 13:19:21
---

#### Loading files into tables

Hive 在加载数据进表的时候不会做任何转换。Load 操作只是纯粹将数据文件复制/移动到 Hive 表关联的位置。

> 注意：Hive 表字段分隔符必须与文件中数据字段分隔符一致。

<!-- more -->

##### 语法

    LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)]

##### 简要说明

- *filepath* 可以是：
  - 一个相对路径，如：project/data1
  - 一个绝对路径，如：/user/hive/project/data1
  - 带有方案和授权（可选）的完整 URI，如：hdfs://namenode:9000/user/hive/project/data1
- 加载的目标可以是表或者分区。如果表是分区的，则必须指定左右分区列的值来指定这个表特定的分区。
- *filepath* 可以指向一个文件（在这种场景下 Hive 移动这个文件到表中）；或者它是一个目录（在这种场景下 Hive 移动目录下所有的文件到表中）。在两种场景下，*filepath* 指向一个文件集合。
- 如果指定了 LOCAL 关键字：
  - load 命令将在本地文件系统查找 *filepath*。如果指定了一个相对地址，将解释为用户当前工作目录的相对路径。用户也可以为本地文件制定全 URI－如：file:///user/hive/project/data1
  - load 命令会尝试拷贝指定到 *filepath* 下的所有文件到目标文件系统。通过查看表的位置属性来推断目标文件系统。复制的数据文件将移动到这个表中。
- 如果不指定 LOCAL 关键字，Hive 将使用 *filepath* 的全 URI（如果指定了一个），或者应用以下规则：
  - 如果方案和授权没有指定，Hive 将使用指定 Namenode URI 的 hadoop 配置参数 fs.default.name 中的方案和授权。
  - 如果不是绝对路径，Hive 将相对于 /user/<username&gt; 推断路径。
  - Hive 将移动 *filepath* 指向的文件到表（或分区）中。
- 如果使用了 OVERWRITE 关键字，则目标表（或分区）中的内容将被 *filepath* 指向的文件删除和替换；否则 *filepath* 指向的文件将追加到表中。
  - 注意，如果目标表（分区）已经有一个与 *filepath* 下包含的名字冲突的文件，那么已经存在的文件将被新文件替换。

##### 注意

- *filepath* 不能包含子目录。
- 如果不提供 LOCAL 关键字，*filepath* 必须跟指向表（或分区）位置相同的文件系统。
- Hive 只做简单的检查来确保加载的文件跟表匹配。一般检查如果表是以 sequencefile 格式存储的，加载的文件也要是 sequencefile 文件，反过来亦然。
