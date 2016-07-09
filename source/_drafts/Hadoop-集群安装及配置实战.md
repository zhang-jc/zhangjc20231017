title: Hadoop 集群安装及配置实战
tags:
- 大数据
- Hadoop
categories:
- 大数据
- Hadoop
---
### 目的

在三台机器上安装 Hadoop 集群，并配置启动，且成功运行 WordCount 示例程序。

### 准备工作

#### 机器列表

- 三台相同配置的虚拟机：192.168.1.133、192.168.1.134、192.168.1.139
- 主控主机：192.168.1.113

配置了主控主机到三台虚拟机的免密码登陆。主控主机主要用来向三台虚拟机分发文件及批量配置三台虚拟机。

#### 机器配置

三台虚拟机配置如下：

- 操作系统：Ubuntu Server 16.04 LTS
- 内存：2G
- 磁盘：15G

#### 架构

- NameNode：192.168.1.133
- ResourceManager：192.168.1.134
- DataNode：192.168.1.133、192.168.1.134、192.168.1.139

### 配置过程

#### 设置 JAVA_HOME

修改 etc/hadoop/hadoop-env.sh、etc/hadoop/mapred-env.sh、etc/hadoop/yarn-env.sh 三个配置文件中的 JAVA_HOME 参数。

#### 配置 etc/hadoop/core-site.xml

    <configuration>
      <property>
        <name>fs.defaultFS</name>
        <value>hdfs://192.168.1.133:9000</value>
      </property>
    </configuration>

#### etc/hadoop/hdfs-site.xml
