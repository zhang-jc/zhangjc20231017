title: Windows7 上构建并安装 Hadoop 2.7.3
tags:
  - 大数据
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-02-11 22:14:12
---

### 构建 Hadoop Windows 版本

不要尝试从 Cygwin 中运行安装。Cygwin 既不是必须的也不被支持的。

#### 选择 Java 版本并设置 JAVA_HOME

Hadoop 开发者已经测试了 Oracle JDK 1.7 和 1.6，并且已知可以正常工作的版本。

确保设置了 JAVA_HOME，并且不包含任何空字符。

<!-- more -->

#### 获取 Hadoop 源代码

下载 Hadoop 2.7.3 源代码。下载地址：<http://hadoop.apache.org/releases.html>

#### 安装依赖并配置构建环境

请阅读博文“[Window7 搭建 Hadoop-2.7.3 源码阅读环境问题解决列表](http://zhang-jc.github.io/2017/02/11/Window7-%E6%90%AD%E5%BB%BA-Hadoop-2-7-3-%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB%E7%8E%AF%E5%A2%83%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E5%88%97%E8%A1%A8/)”。我环境及依赖列表如下：

- Windows 7
- java version "1.7.0_80"
- Apache Maven 3.2.3
- ProtocolBuffer 2.5.0
- cmake version 3.7.2 win64 x64
- Windows SDK 7.1

#### 构建并拷贝打包文件

在源代码根目录下运行下面的命令构建二进制发布代码。

    mvn package -Pdist,native-win -DskipTests -Dtar

注意这个命令像 BUILDING.txt 文档中要求的必须从 Windows SDK command prompt 窗口运行。成功构建后会在 hadoop-dist\target\ 目录下生成一个 hadoop.tar.gz 二进制包。

Hadoop 版本号会出现在包文件名中。构建不同的版本则报名也会不一样。

#### 安装

选择一个安装的目标目录。用 c:\deploy 作为示例。解压 tar.gz 文件（hadoop-2.7.3.tar.gz）到 c:\deploy 下。这将生成一个如下结构的目录。如果安装一个多节点的集群，那么在每台节点上重复该步骤。

    C:\deploy>dir
     Volume in drive C has no label.
     Volume Serial Number is 9D1F-7BAC

     Directory of C:\deploy

    01/18/2014  08:11 AM    <DIR>          .
    01/18/2014  08:11 AM    <DIR>          ..
    01/18/2014  08:28 AM    <DIR>          bin
    01/18/2014  08:28 AM    <DIR>          etc
    01/18/2014  08:28 AM    <DIR>          include
    01/18/2014  08:28 AM    <DIR>          libexec
    01/18/2014  08:28 AM    <DIR>          sbin
    01/18/2014  08:28 AM    <DIR>          share
                   0 File(s)              0 bytes

### 启动一个单节点（伪分布式）集群
#### HDFS 配置示例

在可以启动 Hadoop 守护进程之前，需要编辑几个配置文件。配置文件模板可以在 c:\deploy\etc\hadoop 下找到，假设你的安装目录是 c:\deploy。

首先编辑文件 hadoop-env.cmd，在文件末尾添加下面的内容：

    set HADOOP_PREFIX=c:\deploy
    set HADOOP_CONF_DIR=%HADOOP_PREFIX%\etc\hadoop
    set YARN_CONF_DIR=%HADOOP_CONF_DIR%
    set PATH=%PATH%;%HADOOP_PREFIX%\bin

编辑或创建文件 core-site.xml，并确保文件有下面的配置：

    <configuration>
      <property>
        <name>fs.default.name</name>
        <value>hdfs://0.0.0.0:19000</value>
      </property>
    </configuration>

编辑或创建文件 hdfs-site.xml，并添加下面的配置：

    <configuration>
      <property>
        <name>dfs.replication</name>
        <value>1</value>
      </property>
    </configuration>

最后，编辑或创建文件 slaves，并确保有下面的配置：

    localhost

按照默认配置 HDFS 元数据及数据文件放在当前磁盘的 \tmp 目录下。在上面的示例中这个目录是 c:\tmp。作为第一次测试安装可以保留默认配置。

#### YARN 配置示例

编辑或创建 %HADOOP_PREFIX%\etc\hadoop 下的文件 mapred-site.xml，并添加下面的配置，用你的 Windows 用户名替换 %USERNAME%。

    <configuration>

       <property>
         <name>mapreduce.job.user.name</name>
         <value>%USERNAME%</value>
       </property>

       <property>
         <name>mapreduce.framework.name</name>
         <value>yarn</value>
       </property>

      <property>
        <name>yarn.apps.stagingDir</name>
        <value>/user/%USERNAME%/staging</value>
      </property>

      <property>
        <name>mapreduce.jobtracker.address</name>
        <value>local</value>
      </property>

    </configuration>

最后，编辑或创建文件 yarn-site.xml，并添加下面的配置：

    <configuration>
      <property>
        <name>yarn.server.resourcemanager.address</name>
        <value>0.0.0.0:8020</value>
      </property>

      <property>
        <name>yarn.server.resourcemanager.application.expiry.interval</name>
        <value>60000</value>
      </property>

      <property>
        <name>yarn.server.nodemanager.address</name>
        <value>0.0.0.0:45454</value>
      </property>

      <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
      </property>

      <property>
        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
      </property>

      <property>
        <name>yarn.server.nodemanager.remote-app-log-dir</name>
        <value>/app-logs</value>
      </property>

      <property>
        <name>yarn.nodemanager.log-dirs</name>
        <value>/dep/logs/userlogs</value>
      </property>

      <property>
        <name>yarn.server.mapreduce-appmanager.attempt-listener.bindAddress</name>
        <value>0.0.0.0</value>
      </property>

      <property>
        <name>yarn.server.mapreduce-appmanager.client-service.bindAddress</name>
        <value>0.0.0.0</value>
      </property>

      <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
      </property>

      <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>-1</value>
      </property>

      <property>
        <name>yarn.application.classpath</name>
        <value>%HADOOP_CONF_DIR%,%HADOOP_COMMON_HOME%/share/hadoop/common/*,%HADOOP_COMMON_HOME%/share/hadoop/common/lib/*,%HADOOP_HDFS_HOME%/share/hadoop/hdfs/*,%HADOOP_HDFS_HOME%/share/hadoop/hdfs/lib/*,%HADOOP_MAPRED_HOME%/share/hadoop/mapreduce/*,%HADOOP_MAPRED_HOME%/share/hadoop/mapreduce/lib/*,%HADOOP_YARN_HOME%/share/hadoop/yarn/*,%HADOOP_YARN_HOME%/share/hadoop/yarn/lib/*</value>
      </property>
    </configuration>

#### 初始化环境变量

运行 c:\deploy\etc\hadoop\hadoop-env.cmd 设置启动脚本及守护进程使用的环境变量。

#### 格式化文件系统

用下面的命令格式化文件系统：

    %HADOOP_PREFIX%\bin\hdfs namenode -format

这个命令将打印文件系统的参数。查找下面的两个字符串确保格式化名称执行成功。

    14/01/18 08:36:23 INFO namenode.FSImage: Saving image file \tmp\hadoop-username\dfs\name\current\fsimage.ckpt_0000000000000000000 using no compression
    14/01/18 08:36:23 INFO namenode.FSImage: Image file \tmp\hadoop-username\dfs\name\current\fsimage.ckpt_0000000000000000000 of size 200 bytes saved in 0 seconds.

#### 启动 HDFS 守护进程

运行下面的命令在本机启动 NameNode 和 DataNode。

    %HADOOP_PREFIX%\sbin\start-dfs.cmd

为了验证 HDFS 守护进程已经运行，试着拷贝一个文件到 HDFS。

    C:\deploy>%HADOOP_PREFIX%\bin\hdfs dfs -put myfile.txt /

    C:\deploy>%HADOOP_PREFIX%\bin\hdfs dfs -ls /
    Found 1 items
    drwxr-xr-x   - username supergroup          4640 2014-01-18 08:40 /myfile.txt

    C:\deploy>

#### 启动 YARN 守护进程并运行一个 YARN 任务

最后，启动 YARN 守护进程。

    %HADOOP_PREFIX%\sbin\start-yarn.cmd

集群应该已经启动并运行了。为了验证，我们可以在刚拷贝到 HDFS 上的文件上运行一个 wordcount 的示例任务。

    %HADOOP_PREFIX%\bin\yarn jar %HADOOP_PREFIX%\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.5.0.jar wordcount /myfile.txt /out

### 远程调试

现在可以在自己的个人电脑上跟踪 Hadoop 源代码，并远程调试本机上的 Hadoop。具体做法阅读我的另外一篇博文“[调试 Hadoop 源代码](http://zhang-jc.github.io/2016/09/11/%E8%B0%83%E8%AF%95-Hadoop-%E6%BA%90%E4%BB%A3%E7%A0%81/)”。
