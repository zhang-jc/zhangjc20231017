title: 在 Linux 平台上安装 64 位 JDK
tags:
  - Java
categories:
  - 语言
  - Java
date: 2016-06-20 23:38:53
---

这个过程使用二进制打包文件（.tar.gz）为 64 位 Linux 安装 Java 开发包（JDK）。

<!-- more -->

本教程使用文件：jdk-8uversion-linux-x64.tar.gz

1. 下载文件

下载地址：<http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html>

下载前必须先同意许可协议。这个二进制文件可以被所有用户安装（不只是 root 用户）在任何有写入权限的位置。但是，只有 root 用户可以安装 JDK 到系统位置。

2. 切换目录到要安装 JDK 的位置，然后移动二进制 .tar.gz 文件到当前目录。

3. 打开 tar 包并安装 JDK

    # tar zxvf jdk-8uversion-linux-x64.tar.gz

Java 开发包文件被安装在当前目录下叫 jdk1.8.0_version 的目录中。

4. 如果想节省磁盘空间则删除 .tar.gz 文件。

5. 配置 Path

使用 vi 编辑 profile 文件：

    # vi /etc/profile

添加以下内容：

    JAVA_HOME=/usr/local/lib/jdk1.8.0_91
    export JAVA_HOME

    PATH=$PATH:$JAVA_HOME/bin
    export PATH

重新登陆或者使用 source 命令生效最新配置：

    source /etc/profile

6. 检查安装结果

    # java -version
    ava version "1.8.0_91"
    Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
    Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)

如果看到类似上面的信息则说明安装成功。
