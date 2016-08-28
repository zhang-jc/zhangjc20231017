title: 搭建 Hadoop 源代码阅读环境
tags:
- Hadoop
- Eclipse
- Maven
categories:
- 大数据
- Hadoop
---

### 环境

- Mac OS X EI Capitan 10.11.6
- Java version "1.8.0_91"
- git version 2.7.4 (Apple Git-66)
- Apache Maven 3.3.9

### 下载源代码

从 Git 上下载最新源代码：

    git clone git://git.apache.org/hadoop-common.git

### 构建代码

构建代码，使项目可以导入到 Eclipse 中。切换目录到 hadoop-common，执行以下命令：

    $ mvn install -DskipTests
    $ mvn eclipse:eclipse -DdownloadSources=true -DdownloadJavadocs=true

### 错误列表

#### 错误: 程序包com.sun.javadoc不存在

    [INFO] -------------------------------------------------------------
    [ERROR] COMPILATION ERROR : 
    [INFO] -------------------------------------------------------------
    [ERROR] /Users/ling/work/git/hadoop-common/hadoop-common-project/hadoop-annotations/src/main/java/org/apache/hadoop/classification/tools/ExcludePrivateAnnotationsStandardDoclet.java:[20,22] 错误: 程序包com.sun.javadoc不存在

错误原因是 CLASSPATH 未配置。