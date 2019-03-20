title: 'Jenkins protoc: command not found'
date: 2019-03-20 12:07:39
tags:
- Jenkins
categories:
- 开发工具
- Jenkins
---
在使用 Jenkins 编译 Hadoop3.1.2 时报错信息如下：

    [INFO] --- hadoop-maven-plugins:3.1.2:protoc (compile-protoc) @ hadoop-common ---
    [WARNING] [protoc, --version] failed: java.io.IOException: Cannot run program "protoc": error=13, 权限不够
    [ERROR] stdout: []
    [INFO] ------------------------------------------------------------------------

<!-- more -->

原因是 Jenkins 中找不到 protoc 命令。解决方法是在 Jenkins 中配置环境变量 PATH 指定 protoc 路径。
![jenkins path](/uploads/20190320/jenkins-path.png)

再次执行发现以下错误：

    protoc: error while loading shared libraries: libprotobuf.so.8: cannot open shared object file: No such file or directory

解决方法跟 PATH 配置一样道理，配置一个新的环境变量 LD_LIBRARY_PATH：
![jenkins LD_LIBRARY_PATH](/uploads/20190320/jenkins-LD_LIBRARY_PATH.png)
