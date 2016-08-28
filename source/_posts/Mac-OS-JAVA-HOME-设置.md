title: Mac OS JAVA_HOME 设置
tags:
  - Java
  - JDK
  - Mac OS
categories:
  - 语言
  - Java
date: 2016-08-28 11:36:14
---


在 Mac OS 上使用 DMG 文件安装了 [Jdk8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 之后，在默认路径下找不到 JDK 的 HOME 路径：

    $ which java
    /usr/bin/java
    $ ls -l /usr/bin/java
    lrwxr-xr-x  1 root  wheel  74 12  6  2015 /usr/bin/java -> /System/Library/Frameworks/JavaVM.framework/Versions/Current/Commands/java
    $ ls -l /System/Library/Frameworks/JavaVM.framework/Versions
    total 8
    drwxr-xr-x  10 root  wheel  340  5  9 20:45 A
    lrwxr-xr-x   1 root  wheel    1 12  6  2015 Current -> A
    $ ls -l /System/Library/Frameworks/JavaVM.framework/Versions/A/
    total 80
    drwxr-xr-x  47 root  wheel    1598 10 18  2015 Commands
    drwxr-xr-x   4 root  wheel     136 10 18  2015 Frameworks
    drwxr-xr-x  14 root  wheel     476  8  2  2015 Headers
    drwxr-xr-x   3 root  wheel     102  8 23  2015 JavaPluginCocoa.bundle
    -rwxr-xr-x   1 root  wheel  109488 10 18  2015 JavaVM
    drwxr-xr-x   3 root  wheel     102  8  2  2015 Modules
    drwxr-xr-x  45 root  wheel    1530 10 18  2015 Resources
    drwxr-xr-x   3 root  wheel     102  8 23  2015 _CodeSignature

在 Mac OS 下可以使用 /usr/libexec/java_home 命令来定位 JAVA_HOME：

    $ /usr/libexec/java_home 
    /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home

设置 JAVA_HOME：

    export JAVA_HOME=`/usr/libexec/java_home`

检查 JAVA_HOME：

    $ echo $JAVA_HOME
    /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home

如果安装了多个版本的 JDK，可以使用 -V 命令选项列出所有版本的 JAVA_HOME：

    $ /usr/libexec/java_home -V
    Matching Java Virtual Machines (1):
        1.8.0_91, x86_64:	"Java SE 8"	/Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home

    /Library/Java/JavaVirtualMachines/jdk1.8.0_91.jdk/Contents/Home