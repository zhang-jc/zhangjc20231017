title: 搭建 Hadoop 源代码阅读环境
tags:
  - Hadoop
  - Eclipse
  - Maven
categories:
  - 大数据
  - Hadoop
date: 2016-08-29 02:11:42
---


### 环境

- Mac OS X EI Capitan 10.11.6
- java version "1.7.0_80"
- git version 2.7.4 (Apple Git-66)
- Apache Maven 3.3.9

<!-- more -->

### 下载源代码

从 Git 上下载最新源代码：

    git clone git://git.apache.org/hadoop-common.git

### 构建代码

构建代码，使项目可以导入到 Eclipse 中。切换目录到 hadoop-common，执行以下命令：

    $ mvn install -DskipTests
    $ mvn eclipse:eclipse -DdownloadSources=true -DdownloadJavadocs=true

> 这个过程时间比较久，最好有内部的 Nexus 服务器，不然可能会不够顺畅。

### Hadoop 2.7.3 源码环境

下载地址：<http://hadoop.apache.org/releases.html>。下载 2.7.3 源代码包到本地。使用以下命令解压：

    $ tar xzvf hadoop-2.7.3-src.tar.gz

> 解压目录下的 hadoop-2.7.3-src/BUILDING.txt 文件提供了很多信息。

源码构建方式同上。

### Eclipse 中的操作

#### Common

- File -> Import...
- Choose "Existing Projects into Workspace"
- Select the hadoop-common-project directory as the root directory
- Select the hadoop-annotations, hadoop-auth, hadoop-auth-examples, hadoop-nfs and hadoop-common projects
- Click "Finish"
- File -> Import...
- Choose "Existing Projects into Workspace"
- Select the hadoop-assemblies directory as the root directory
- Select the hadoop-assemblies project
- Click "Finish"
- To get the projects to build cleanly:
- * Add target/generated-test-sources/java as a source directory for hadoop-common
- * You may have to add then remove the JRE System Library to avoid errors due to access restrictions

最后一个操作的步骤：

1. Go to the Build Path settings in the project properties.
2. Remove the JRE System Library
3. Add it back; Select "Add Library" and select the JRE System Library. The default worked for me.

#### HDFS

- File -> Import...
- Choose "Existing Projects into Workspace"
- Select the hadoop-hdfs-project directory as the root directory
- Select the hadoop-hdfs project
- Click "Finish"

#### YARN

- File -> Import...
- Choose "Existing Projects into Workspace"
- Select the hadoop-yarn-project directory as the root directory
- Select the hadoop-yarn-project project
- Click "Finish"

#### MapReduce

- File -> Import...
- Choose "Existing Projects into Workspace"
- Select the hadoop-mapreduce-project directory as the root directory
- Select the hadoop-mapreduce-project project
- Click "Finish"

### 错误

#### 错误: 程序包com.sun.javadoc不存在

如果使用 JDK8 执行 mvn install -DskipTests 的话会报一下错误，需要替换为 JDK7 后再执行。

    [INFO] -------------------------------------------------------------
    [ERROR] COMPILATION ERROR :
    [INFO] -------------------------------------------------------------
    [ERROR] /Users/ling/work/git/hadoop-common/hadoop-common-project/hadoop-annotations/src/main/java/org/apache/hadoop/classification/tools/ExcludePrivateAnnotationsStandardDoclet.java:[20,22] 错误: 程序包com.sun.javadoc不存在

#### 'protoc --version' did not return a version

错误信息如下：

    [ERROR] Failed to execute goal org.apache.hadoop:hadoop-maven-plugins:3.0.0-SNAPSHOT:protoc (compile-protoc) on project hadoop-common: org.apache.maven.plugin.MojoExecutionException: 'protoc --version' did not return a version -> [Help 1]

这是因为没有安装 protoc 的缘故。我安装了最新版的 protoc3.0.0 重新执行报错信息如下：

    [ERROR] Failed to execute goal org.apache.hadoop:hadoop-maven-plugins:3.0.0-SNAPSHOT:protoc (compile-protoc) on project hadoop-common: org.apache.maven.plugin.MojoExecutionException: protoc version is 'libprotoc 3.0.0', expected version is '2.5.0' -> [Help 1]

安装 protoc2.5.0 版本后重新执行成功。

#### hadoop-common 编译错误：Type AvroRecord cannot be resolved to a type

- 下载 [avro-tools](http://mirrors.cnnic.cn/apache/avro/avro-1.7.7/java/avro-tools-1.7.7.jar) 最新版。
- 进入源码目录 hadoop-2.7.3-src/hadoop-common-project/hadoop-common/src/test/avro，执行以下命令：

    $ java -jar <所在目录>/avro-tools-1.7.7.jar compile schema avroRecord.avsc ../java/

其中 avsc 文件是 avro 的模式文件，上面命令是要通过模式文件生成相应的 .java 文件。
- 右键单击 eclipse 中的 hadoop-common 项目，然后 refresh。

> 注意，avro-tools 不要下载最新版，要下载 1.7.7 版本；最新 1.8.1 版本测试失败。

#### hadoop-common 编译错误：Type EchoRequestProto cannot be resolved

- 进入源码目录 hadoop-2.7.3-src/hadoop-common-project/hadoop-common/src/test/proto，执行以下命令：

    $ protoc \-\-java_out=../java *.proto

- 右键单击 eclipse 中的 hadoop-common，然后 refresh。
