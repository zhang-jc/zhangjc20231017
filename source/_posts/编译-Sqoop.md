title: 编译 Sqoop
tags:
  - Sqoop
  - 大数据
categories:
  - 大数据
  - Sqoop
date: 2018-01-16 14:19:47
---


### 基本要求

编译 Sqoop 需要以下工具：

- Sqoop 1.4.6 版本源码
- Apache ant (1.7.1)
- Java JDK 1.6

另外，构建文档还需要下面的工具：

- asciidoc
- make
- python 2.5+
- xmlto
- tar
- gzip

<!-- more -->

此外，可以用以下工具测试 Sqoop 的构建：

- findbugs (1.3.9)：检查代码质量
- cobertura (1.9.4.1)：检查代码覆盖率
- checkstyle (5.x)：检查代码格式

### 编译过程

Sqoop 是用 ant 编译的。输入“ant -p”查看可以获得的构建目标列表。

数据“ant”编译所有的 java 源码。然后可以用“bin/sqoop”运行 Sqoop。

如果想构建所有的东西（包含文档），运行“ant package”。结果将出现在“build/sqoop-(version)/”目录。
