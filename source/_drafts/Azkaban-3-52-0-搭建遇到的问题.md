title: Azkaban 3.52.0 搭建遇到的问题
tags:
- 大数据
- Azkaban
categories:
- 大数据
- Azkaban
---
### Mysql数据初始化

如果 MySQL 使用库表字符集使用 UTF-8，在初始化 Azkaban 的 MySQL 库时报错如下：

    ERROR 1071 (42000): Specified key was too long; max key length is 767 bytes
    ERROR 1146 (42S02): Table 'azkaban3.execution_jobs' doesn't exist
    ERROR 1071 (42000): Specified key was too long; max key length is 767 bytes
    ERROR 1146 (42S02): Table 'azkaban3.execution_logs' doesn't exist
    ERROR 1146 (42S02): Table 'azkaban3.execution_logs' doesn't exist
    ERROR 1146 (42S02): Table 'azkaban3.execution_logs' doesn't exist
    
这个是因为表 execution_logs 的主键总的长度字符超过 767 个字节导致的。解决方法是将该表的编码改为 Latin1。

### 
