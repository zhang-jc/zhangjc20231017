title: Hive 2.1.1 编译安装
tags:
  - 大数据
  - Hive
categories:
  - 大数据
  - Hive
date: 2017-06-11 21:12:41
---

### 软件要求

- Java 1.7 或更新版本。强烈建议使用 Java 1.8
- Hadoop 2.x

### 编译打包

    mvn clean package -Pdist -DskipTests
    
编译完成后，打包后的 .tar.gz 文件在 packaging/target 下。

<!-- more -->

### 安装 Hive

拷贝编译打包后的 packaging/target/apache-hive-2.1.1-bin.tar.gz 到 /opt/ 目录下，并解压：

    $ cd /opt
    $ tar xzvf apache-hive-2.1.1-bin.tar.gz
    $ chown -R hive:hive apache-hive-2.1.1
    $ ln -s apache-hive-2.1.1 hive
    $ chown -h hive:hive hive

设置环境变量：

    export HIVE_HOME=/opt/hive
    export PATH=$HIVE_HOME/bin:$PATH

### 配置 Hive

在 hive 的 home 路径下的 conf 目录下创建 hive-site.xml 文件，并配置以下内容：

    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
    <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
    <configuration>
      <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
      </property> 
      <property>
        <name>hive.exec.scratchdir</name>
        <value>/tmp/hive</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://127.0.0.1/hive?createDatabaseIfNotExist=true</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>hive</value>
      </property>
      <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value></value>
      </property>
      <property>
        <name>hive.auto.convert.join</name>
        <value>false</value>
      </property>
    </configuration>

下载 MySQL 的 Java 驱动并放到 lib 下。

### 运行 Hive

Hive 使用 Hadoop，所以 path 中必须有 Hadoop 或者 export HADOOP_HOME=<hadoop-install-dir>。另外，需要使用下面的命令创建 /tmp 和 /user/hive/warehouse，并且设置 chmod g+w，这样才可以在 hive 中创建表。

    $ hadoop fs -mkdir /tmp
    $ hadoop fs -mkdir -p /user/hive/warehouse
    $ hadoop fs -chmod g+w /tmp
    $ hadoop fs -chmod g+w /user/hive/warehouse

使用以下命令初始化 hive 元数据库：

    $ schematool -initSchema -dbType mysql

使用以下命令进入 hive 命令行模式：

    $ hive
    hive>
