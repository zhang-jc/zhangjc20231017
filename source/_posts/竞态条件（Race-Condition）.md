---
title: 竞态条件（Race Condition）
date: 2021-12-06 17:31:29
tags:
- 概念
- 计算机理论
- 多线程
- 分布式
categories:
- 计算机理论
- 概念
---

> 本文翻译自：[Race Condition](https://techterms.com/definition/race_condition)

当软件程序依赖于一个或多个进程正确运行的时间时，就会出现竞态条件。如果线程在意外的时间运行或完成，则可能会导致不可预测的行为，例如错误输出或程序死锁。

> A race condition occurs when a software program depends on the timing of one or more processes to function correctly. If a thread runs or finishes at an unexpected time, it may cause unpredictable behavior, such as incorrect output or a program deadlock.

大多数软件程序都是多线程的，这意味着它们可以同时处理多个线程。一个编写良好的应用程序将确保每个线程的结果都按照预期的顺序进行处理。如果程序依赖于以不可预测的顺序运行的线程，则可能会出现竞态条件。

> Most software programs are multithreaded, meaning they can process several threads at once. A well-programmed application will ensure the results of each thread are processed in the expected order. If a program relies on threads that run in an unpredictable sequence, a race condition may occur.

一个简单的例子是处理布尔值的逻辑门。AND 逻辑门有两个输入和一个输出。如果输入 A 和 B 为真，则 AND 门产生真。如果一个或两个输入为假，则生成 FALSE。如果程序在加载变量 A 和 B 之前检查逻辑门结果，则可能会发生竞态条件。正确的流程应该是：

> A simple example is a logic gate that handles boolean values. The AND logic gate has two inputs and one output. If inputs A and B are true, the AND gate produces TRUE. If one or both inputs are false, it produces FALSE. A race condition may happen if a program checks the logic gate result before variables A and B are loaded. The correct process would be:

1. 加载变量 A
2. 加载变量 B
3. 检查 AND 逻辑门结果

> 1. Load variable A
> 2. Load variable B
> 3. Check result of the AND logic gate

不正确的顺序是：

> An incorrect sequence would be:

1. 加载变量 A
2. 检查 AND 逻辑门结果
3. 加载变量 B

> 1. Load variable A
> 2. Check result of the AND logic gate
> 3. Load variable B

上面第二个例子的结果可能与第一个例子相同，也可能不同。例如，变量 B 在加载前后可能为 FALSE，这不会改变结果。如果 A 是 FALSE，那么 B 是 TRUE 还是 FALSE 都没有关系。但是，如果 A 和 B 都为真，则结果应该为真。在检查逻辑门的结果后加载变量 B 会产生错误的 FALSE 结果。

> The result of the second example above may or may not be the same as the first example. For instance, variable B may be FALSE before and after it is loaded, which would not change the result. If A is FALSE, it does not matter whether or not B is TRUE or FALSE. However, if both A and B are true, the result should be TRUE. Loading variable B after checking the result of the logic gate would produce an incorrect result of FALSE.

竞态条件产生的不一致的输出可能会导致难以检测的错误。程序员可以通过确保以一致的顺序处理线程来避免这些问题。

> The inconsistent output produced by race conditions may cause bugs that are difficult to detect. Programmers can avoid these issues by ensuring threads are processed in a consistent sequence.