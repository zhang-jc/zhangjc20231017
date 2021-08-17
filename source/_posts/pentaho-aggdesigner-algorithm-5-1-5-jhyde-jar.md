---
title: pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar
date: 2021-08-17 15:04:04
tags:
- Maven
- Java
categories:
- 开发工具
- Maven
---

Maven编译时无法自动下载[pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar](/images/20210817/pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar)，需要手动下载并安装到本地仓库。安装命令：

```Shell
mvn install:install-file -Dfile=./pentaho-aggdesigner-algorithm-5.1.5-jhyde.jar -DgroupId=org.pentaho -DartifactId=pentaho-aggdesigner-algorithm -Dversion=5.1.5-jhyde -Dpackaging=jar
```