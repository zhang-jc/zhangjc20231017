title: Sqoop 支持 ORC 文件格式
tags:
  - Hive
  - ORC
categories:
  - 大数据
  - Hive
date: 2018-12-28 01:18:34
---


### ORC 介绍
   ORC 文件格式是 Hive 0.11.0 版本引入的一种文件格式。ORC 的引入是为了解决其他 Hive 文件格式的局限性。使用 ORC 文件格式提升 Hive 读取、写入及处理数据的性能。

<!-- more -->

   与 RCFile 对比，ORC 文件格式有很多优点：
   - 每个 Task 只输出一个文件，降低 NameNode 的负载。
   - Hive 数据类型支持，包括：datetime、decimal 以及复杂数据类型（struct、list、map、union）。
   - 文件中存储轻量级的索引：
     - 跳过不通过谓语过滤的行组
     - 跳转到指定的行
   - 基于数据类型的块模式压缩：
     - 整型数据列采用行程长度编码（run-length encoding）
     - 字符串数据列采用词典编码（dictionary encoding）
   - 使用独立的 RecordReader 并发读取相同的文件
   - 无需扫描 markers 就可以分割文件的能力
   - 绑定读写需要的内存量
   - 使用 Protocol Buffer 存储元数据，允许添加、移除字段

   Hive 官网介绍：https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC

### Sqoop 支持 ORC

   通过 Sqoop-HCatalog 集成解决 Sqoop 不支持 ORC 的问题。

#### HCatalog 背景

    HCatalog 是 Hadoop 的一个 table 与存储管理的一个服务，用户可以更容易地使用不同的数据处理工具 Pig、MapReduce 和 Hive 读写数据。HCatalog 表的抽象呈现给用户一个 HDFS 分布式文件系统（HDFS）中的关系视图，用户不需要担心数据存储在哪里及数据的存储格式：RCFile 格式、text 文件、或者 SequenceFile。

    HCatalog 支持读写任何实现了 Hive SerDe（serializer-deserializer）的文件格式。默认的，HCatalog 支持 RCFile、CSV、JSON 和 SequenceFile。要使用用户自定义格式，必须提供 InputFormat 和 OutputFormat 及 SerDe。

    Sqoop 使用 HCatalog 抽象不同存储格式的能力来支持 RCFile（以及未来的文件类型）。

#### 集成 HCatalog 后新增的参数

    见 Sqoop 官方文档：http://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html#_sqoop_hcatalog_integration

#### 定制 Sqoop 改造
    修改 bin/sqoop 命令脚本：为 import 操作增加 Hive Table 分区数据删除逻辑，在执行导入前不需要另行清理数据，从而简化 Sqoop import 脚本的开发工作。

#### Sqoop 导入 ORC 实例
##### 第一步：创建 Hive 表
    CREATE TABLE `dev.bims_device`(
      `code` string,
      `mac` string,
      `wifi_mac` string,
      `create_date` string,
      `activate_date` string,
      `state` string,
      `area_id` bigint,
      `city_id` bigint,
      `sp_define_id` bigint)
    PARTITIONED BY (
      `dt` string)
    stored as orc;
##### 第二步：Sqoop import
    sqoop import \
      --hcatalog-database dev \
      --hcatalog-table bims_device \
      --hcatalog-partition-keys dt \
      --hcatalog-partition-values ${dt} \
      --connect jdbc:mysql://${host}:${port}/bims \
      --username "${userName}" \
      --password "${password}" \
      --table bims_device \
      --columns "code,mac,wifi_mac,create_date,activate_date,state,area_id,city_id,sp_define_id"
#### Sqoop 导出 ORC 实例
##### 第一步：创建 MySQL 数据表
    CREATE TABLE `test`.`bims_device`(
      `code` varchar(50),
      `mac` varchar(50),
      `wifi_mac` varchar(50),
      `create_date` varchar(50),
      `activate_date` varchar(50),
      `state` varchar(50),
      `area_id` bigint,
      `city_id` bigint,
      `sp_define_id` bigint);
##### 第二步：Sqoop export
    sqoop export \
      --hcatalog-database dev \
      --hcatalog-table bims_device \
      --hcatalog-partition-keys dt \
      --hcatalog-partition-values ${dt} \
      --connect jdbc:mysql://${host}:${port}/test?useCompression=true \
      --username ${userName} \
      --password ${password} \
      --table bims_device
### 注意事项
     - MySQL 表字段名称必须与 Hive 仓库中的表字段名称一致
     - Sqoop ORC 导出时，数据类型需要转换时如果存在脏数据会导致导出失败。例如，长度为 0 的 String 如果导出为 int 时。
     - 导出时需要提前清理 MySQL 中之前导出的数据，避免重复运行时造成数据重复。
