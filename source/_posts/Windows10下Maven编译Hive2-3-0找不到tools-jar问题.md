---
title: Windows10下Maven编译Hive2.3.0找不到tools.jar问题
date: 2022-02-09 09:16:31
tags:
- Maven
- Java
- Hive
- 大数据
categories:
- 大数据
- Hive
---

详细报错信息如图：![tools.jar not found](/images/20220209/tools-jar-not-found.jpg)

在Hive源代码目录下搜索tools.jar发现没有指明特定目录的配置。如下：![Hive源代码搜索结果](/images/20220209/hive-pom-search.jpg)

怀疑是Maven配置或者依赖包中存在指定的情况。在.m2目录下搜索：![Maven本地仓库搜索结果](/images/20220209/m2-search.jpg)

从上面的搜索可以看到很多从${JAVA_HOME}下查找该包的配置。检查环境变量${JAVA_HOME}发现未配置，错误原因就出在这里。

配置JAVA_HOME后问题解决。