title: javap
tags:
  - Java
  - JDK
categories:
  - 语言
  - Java
date: 2016-10-16 00:28:10
---

反汇编一个或多个 class 文件。

<!-- more -->

### 摘要

**javap** [*options*] *classfile...*

#### *options*

命令行选项。具体选项参见“选项”一节。

#### *classfile*

一个或多个用空格分隔的被注释的类，例如：DocFooter.class。可以通过文件名或者 URL（如：file:///home/user/myproject/src/DocFooter.class）指定一个在类路径中的类。

### 描述

*javap* 命令反汇编一个或多个类文件。输出依赖于使用的选项。当不使用选项时，*javap* 命令打印传递给它的类的包、protected 和 public 属性、方法。*javap* 命令打印输出到标准输入。

### 选项

#### -help --help -?

打印 *javap* 命令的帮助信息。

#### -version

打印发布信息。

#### -l

输出行号和本地变量表。

#### -public

仅显示公共类和成员。

#### -protected

显示受保护的/公共类和成员。

#### -private -p

显示所有类和成员。

#### -Joption

传递指定的选项给 JVM。例如：

    javap -J-version
    javap -J-Djava.security.manager -J-Djava.security.policy=MyPolicy MyClassName

关于 JVM 选项的更多信息，参见 [java](http://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html#CBBFHAJA) 命令文档。

#### -s

输出内部类型签名。

#### -sysinfo

显示正在处理的类的系统信息 (路径、大小、日期、MD5 散列)。

#### -constants

显示静态最终常量。

#### -c

对代码进行反汇编。

#### -verbose

打印方法参数和本地变量的数量以及栈区大小。

#### -classpath *path*

指定查找用户类文件的位。当设置了这个变量将覆盖默认的或 CLASSPATH 环境变量。

#### -bootclasspath *path*

覆盖引导类文件的位置。默认的，引导类是那些实现了位于 jre/lib/rt.jar 和其他几个 JAR 文件的核心 Java 平台的类。

#### -extdir *dirs*

指定javap搜索已安装的java扩展的位置，默认的java扩展的位置为 java.ext.dirs 的值。

### 实例

编译下面的 HelloWorld.java 类：

    public class HelloWorld {

      public static void main(String[] args) {
        System.out.println("Hello World!");
      }
    }

javap HelloWorld.class 命令的输出如下：

    Compiled from "HelloWorld.java"
    public class HelloWorld {
      public HelloWorld();
      public static void main(java.lang.String[]);
    }

javap -c HelloWorld.class 命令的输出如下：

    Compiled from "HelloWorld.java"
    public class HelloWorld {
      public HelloWorld();
        Code:
           0: aload_0       
           1: invokespecial #1                  // Method java/lang/Object."<init>":()V
           4: return        

      public static void main(java.lang.String[]);
        Code:
           0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
           3: ldc           #3                  // String Hello World!
           5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
           8: return        
    }