title: Java 单例模式
tags:
  - 设计模式
  - Java
  - 单例模式
categories:
  - 语言
  - Java
date: 2016-05-13 04:56:38
---

单例模式是 Java 程序设计最常用的设计模式之一。在 Java 语言中，单例模式有几种不同的实现方式。从实例创建时机分为两大类：

- 懒汉式：在需要时才创建类唯一的实例
- 饿汉式：在类文件加载时创建类唯一的实例

<!-- more -->

一、懒汉式

根据有锁、无锁及锁级别的不同可以有三种实现方式。

1、最简单的实现

    public class Singleton {
      private Singleton() {}
      private static Singleton single=null;

      public static Singleton getInstance() {
        if (single == null) {
          single = new Singleton();
        }
        return single;
      }
    }

该方法通过私有化构造方法，并且提供生成类对象的静态 getInstance 方法来保证该类只生成一个实例。

但是，这种方式只能用在非多线程并发的场景。

2、方法锁的实现

为了解决实现 1 不能在多线程场景下使用的问题，给 getInstance 方法做线程同步是一种简单的实现方式。

    public class Singleton {
      private Singleton() {}
      private static Singleton single=null;

      public static synchronized Singleton getInstance() {
        if (single == null) {
          single = new Singleton();
        }
        return single;
      }
    }

该方法解决了多线程同步问题，保证在多线程场景下也只生成一个实例。但每次调用 getInstance 方法都需要做线程间同步会非常浪费资源。只需要在实例还未生成时才需要做线程同步，实例生成以后直接使用即可，所以就有了方法 3 。

3、双重检查锁定

    public class Singleton {
      private Singleton() {}
      private static Singleton single=null;

      public static Singleton getInstance() {
        if (singleton == null) {
          synchronized (Singleton.class) {
            if (singleton == null) {
              singleton = new Singleton();
            }
          }
        }
        return singleton;
      }
    }

该实现方式避免了多线程场景下每次都做线程同步的问题，但在实例生成之前线程间仍然是需要做线程同步的。但该方法还是需要线程之间的同步，如果实例初始化比较复杂或者需要的时间比较长会阻塞很多线程等待。方法 4 完全不需要线程间同步。

4、静态内部类

    public class Singleton {
      private static class LazyHolder {
        private static final Singleton INSTANCE = new Singleton();
      }
      private Singleton (){}
      public static final Singleton getInstance() {
        return LazyHolder.INSTANCE;
      }
    }

该方法通过类加载器在加载 LazyHolder 类并初始化时生成 Singleton 类的实例来保障单例。

> 猜想：该方法并非完全不做线程同步。只不过在多线程场景下，多线程同步是由类加载器或者 JVM 来完成的。只是不需要业务程序代码做线程同步的工作。猜想是否正确还需要做更深的研究。

二、饿汉式

    public class Singleton {
      private Singleton() {}
      private static final Singleton single = new Singleton();

      public static Singleton1 getInstance() {
        return single;
      }
    }

该方法跟懒汉式方法 4 很相似，只是类实例生成时间不同。懒汉式方法 4 类的唯一实例是在调用 getInstance 方法时才会生成；而该方法是在类加载时就会生成类的唯一实例。两种方法都是通过类加载器保障保证单例。

###总结：###

不同的应用场景可以灵活选择实现方法：

- 非多线程场景可以采用懒汉式方法 1 就够了，简单。
- 多线程场景下，如果实例初始化不需要其他参数的时候（生成实例时调用无参构造方法），使用懒汉式方法 4。
- 多线程场景下，如果实例初始化时需要其他参数（生成实例时调用带参数的构造方法），使用懒汉式方法 3。
- 饿汉式的优势是类加载时唯一实例就生成了，多线程使用时可以减少延迟。但即使不使用该实例，类加载时也会生成一个实例在内存中。基本属于空间换时间的情况。内存不敏感的场景比较适合使用。
