title: Java 读取 Properties 文件的方法
tags:
  - Java
categories:
  - 语言
  - Java
date: 2017-09-28 10:00:20
---


Java 读取 Properties 文件有两种简单方法，就是使用 ClassLoader 中的资源读取方法。

- public InputStream getResourceAsStream(String name)
    该方法是非静态方法，所以不能在静态代码中使用。
- public static InputStream getSystemResourceAsStream(String name)
    该方法是静态方法，可以在静态代码中使用。 
