title: Azkaban 3.52.0 搭建遇到的问题
tags:
  - 大数据
  - Azkaban
categories:
  - 大数据
  - Azkaban
date: 2018-09-28 14:21:00
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

### 通过 curl 激活 executor

在执行官方文档命令时异常如下：

    $ curl -G "localhost:$(<./executor.port)/executor?action=activate" && echo
    curl: (7) Failed connect to 172.16.72.69:443; No route to host

我的解决方法是从安装目录 azkaban-exec-server-3.52.0 下的文件 executor.port 中找到端口号，然后从浏览器中进行激活。如下图：

(/uploads/20180926/azkaban-executor-activate.png)

### 支持 SSL

如要通过 Azkaban 的 REST API 进行某些操作的话，则需要 Azkaban web server 支持 SSL。

使用下面的命令创建 keystore：

    keytool -keystore keystore -alias azkaban -genkey -keyalg RSA
    
根据提示数据相应的信息即可。命令执行完成后会在当前目录生成 keystore 证书文件，将 keystore 考贝到 Azkaban web server 根目录中。修改 conf/azkaban.properties 中的配置如下：

    # Azkaban Jetty server properties.
    jetty.use.ssl=true
    jetty.maxThreads=25
    jetty.ssl.port=8443
    jetty.keystore=keystore
    jetty.password=azkaban
    jetty.keypassword=azkaban
    jetty.truststore=keystore
    jetty.trustpassword=azkaban
