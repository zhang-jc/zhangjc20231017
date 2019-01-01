title: Sqoop 源码修改：增加落地 HDFS 文件数与 MapTask 数量一致性检查
date: 2018-12-30 17:04:29
tags:
- Sqoop
- 大数据
categories:
- 大数据
- Sqoop
---

本篇是对[记录一次 Sqoop 从 MySQL 导入数据到 Hive 问题的排查经过](http://zhang-jc.github.io/2018/12/11/%E8%AE%B0%E5%BD%95%E4%B8%80%E6%AC%A1-Sqoop-%E4%BB%8E-MySQL-%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE%E5%88%B0-Hive-%E9%97%AE%E9%A2%98%E7%9A%84%E6%8E%92%E6%9F%A5%E7%BB%8F%E8%BF%87/)的补充。

Sqoop 命令通过 bin 下面的脚本调用，调用如下：

    exec ${HADOOP_COMMON_HOME}/bin/hadoop org.apache.sqoop.Sqoop "$@"

<!-- more -->

org.apache.sqoop.Sqoop 是 Sqoop 的入口类，在此主要是解析参数及初始化工具类，然后通过 org.apache.hadoop.util.ToolRunner 类调用对应的工具完成操作。Sqoop 的 Import 操作对应的是 org.apache.sqoop.tool.ImportTool 类。

在 ImportTool 类的 return 代码前增加以下代码：

    int numMappers = options.getNumMappers();

    String hDbName = options.getHCatDatabaseName();
    String hTableName = options.getHCatTableName();
    String hPartKeys = options.getHCatalogPartitionKeys();
    String hPartVals = options.getHCatalogPartitionValues();

    if(isStringNotEmpty(hDbName) && isStringNotEmpty(hTableName) && isStringNotEmpty(hPartKeys) &&     isStringNotEmpty(hPartVals)) {
      String[] partKeys = hPartKeys.split(",");
      String[] partVals = hPartVals.split(",");

      String partPathStr = "";
      if(partKeys.length > 0 && partVals.length == partKeys.length) {
        for(int i = 0; i < partKeys.length; i++) {
          partPathStr += partKeys[i] + "=" + partVals[i] + "/";
        }
      }

      String targetDir = "/user/hive/warehouse/" + hDbName + ".db/" + hTableName + "/" + partPathStr;
      targetDir = targetDir.toLowerCase();
      LOG.info("---------targetDir=" + targetDir);

      try {
        FileSystem fs = FileSystem.get(options.getConf());
        RemoteIterator<LocatedFileStatus> rIter = fs.listFiles(new Path(targetDir), false);

        int fileCount = 0;
        while(rIter.hasNext()) {
          fileCount++;
          rIter.next();
        }

        LOG.info("---------------fileCount=" + fileCount);

        if(numMappers != fileCount) {
          LOG.error("files number in hdfs not equals mapper task number !");
          return 2;
        }
      } catch (IOException e) {
        LOG.error("count files number from hdfs error !");
        e.printStackTrace();
        return 3;
      }
    }

改动只针对 Sqoop 集成 HCatalog 方式导入 ORC 格式的情况。因为我们的数据仓库中都采用的是这种方式。

> 优化：当 MySQL 中记录数特别少时，如少于 4 条记录，则默认 Sqoop 的 MapTask 数量为 4 但其实际执行时因为原始记录数不够则实际执行的 MapTask 数量会跟实际的记录数一致，此时 split 数量跟落地 HDFS 的文件数量一致。所以，可以根据 Sqoop 对应 MR 的实际 split 数量进行判断文件数量。
