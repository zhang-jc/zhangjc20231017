title: Eclipse 导入 Sqoop
tags:
  - Eclipse
  - Sqoop
categories:
  - 大数据
  - Sqoop
date: 2017-11-25 23:37:16
---

Sqoop 是使用 Ant 进行编译的，如果需要导入 Eclipse 首先执行以下命令生成 .project 和 .classpath 文件。

    ant eclipse

操作完成后就可以导入 Eclipse 了。

<!-- more -->

导入 Eclipse 后编译会提示以下错误：

    Project 'sqoop' is missing required library: 'build/classes'
    Project 'sqoop' is missing required library: 'build/test/classes'
    Project 'sqoop' is missing required library: 'build/test/extraconf'
    The project cannot be built until build path errors are resolved
    
执行 ant 命令编译 Sqoop，然后在 Eclipse 中刷新项目，出现如下错误：

    The declared package "org.apache.sqoop.fi" does not match the expected package "aop.org.apache.sqoop.fi"
    
如下图所示配置，问题解决。
![Eclipse Sqoop](/uploads/20171125/eclipse-sqoop-import.png)
