---
title: DataX编译问题
date: 2021-11-08 11:17:02
tags:
- 大数据
- DataX
categories:
- 大数据
- DataX
---
编译过程还算顺利，主要缺少两个Jar包：

- eigenbase-properties-1.1.4.jar
- DataX/oscarwriter/src/main/lib/oscarJDBC.jar

# eigenbase-properties-1.1.4.jar

该Jar在Maven中心仓库已不存在。从本站点下载：[eigenbase-properties-1.1.4.jar](/images/20211108/eigenbase-properties-1.1.4.jar)。

下载后执行以下命令进行安装：

```shell
mvn install:install-file -Dfile=./eigenbase-properties-1.1.4.jar -DgroupId=eigenbase -DartifactId=eigenbase-properties -Dversion=1.1.4 -Dpackaging=jar
```

# DataX/oscarwriter/src/main/lib/oscarJDBC.jar

注释掉DataX/oscarwriter/pom.xml中的以下内容：

```xml
<dependency>
  <groupId>com.oscar</groupId>
  <artifactId>oscar</artifactId>
  <version>7.0.8</version>
  <scope>system</scope>
  <systemPath>${basedir}/src/main/lib/oscarJDBC.jar</systemPath>
</dependency>
```
