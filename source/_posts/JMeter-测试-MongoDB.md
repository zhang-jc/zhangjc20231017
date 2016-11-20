title: JMeter 测试 MongoDB
tags:
  - JMeter
  - MongoDB
  - 软件测试
  - 测试工具
  - benchmark
categories:
  - 软件测试
  - 测试工具
date: 2016-04-17 22:37:11
---


### 简介

**Apache JMeter** 是一个 100% 纯 Java 应用程序，设计来做“客户端/服务器”软件（例如 [Web 应用程序](http://jmeter.apache.org/usermanual/build-web-test-plan.html)）。它可以用来测试静态和动态资源的性能，例如静态文件、Java Servlets、ASP.NET、PHP、CGI 脚本、Java 对象、[数据库](http://jmeter.apache.org/usermanual/build-db-test-plan.html)、[FTP 服务器](http://jmeter.apache.org/usermanual/build-ftp-test-plan.html)，还有更多。JMeter 可以给服务器、网络或对象模拟重负载来测试它的强度或者分析不同负载类型下的综合性能。

<!-- more -->

此外，通过创建[断言](http://jmeter.apache.org/usermanual/test_plan.html#assertions)脚本验证应用程序是否返回期望的结果，JMeter可以帮助回归测试你的应用程序。为了最大的灵活性，JMeter 允许用正则表达式创建断言。

但是请注意，JMeter 不是一个浏览器，它在协议层运行。

### 安装

下载网址：<http://jmeter.apache.org/download_jmeter.cgi>。首先正确安装 JRE/JDK，并且设置了 JAVA_HOME 变量。解压下载的文件包到安装目录。

### 运行 JMeter

运行 bin 目录下的 jmeter 启动 JMeter，很快 JMeter 的图形界面就会出现。

### 创建数据库测试计划

本文创建 50 个用户向 MongoDB 服务器发送同一个 Collection 的 Insert Document 请求各 1000 次。因此，总共会像 MongoDB 发送 50 × 1000 = 50000 次 Insert 请求。构建本次测试，需要用到以下组件：[Thread Group](http://jmeter.apache.org/usermanual/test_plan.html#thread_group)、[JDBC Request](http://jmeter.apache.org/usermanual/component_reference.html#JDBC_Request)、[Summary Report](http://jmeter.apache.org/usermanual/component_reference.html#Summary_Report)。

#### 添加用户

用 JMeter 做的所有测试计划的第一步就是添加 [Thread Group](http://jmeter.apache.org/usermanual/test_plan.html#thread_group) 组件。Thread Group 组件告诉 JMeter 模拟多少个用户、多久发送一个请求，以及发送多少次请求。

选中左侧窗格中的测试计划，点击鼠标右键。在弹出的菜单选择菜单项：添加 -> Threads(Users) -> 线程组。添加之后，在右边的窗格就可以看到 Thread Group 的控制面板。配置参数如下：

- 名称：MongoDB Thread Group
- 线程数：50
- Ramp-Up Period (in seconds)：10。该参数告诉 JMeter 延迟多长时间启动所有用户，此处设为 10，JMeter 在 10 秒内完成所有用户的启动，也就是每隔 200 毫秒启动一个用户（10s / 50 = 0.2）。如果设为 0，则 JMeter 会立即启动所有用户。
- 循环次数：1000。这个参数告诉 JMeter 重复多少次测试。要永久重复则选中“永远”选择框。

配置后的参数如图：![Thread Group](/uploads/20160417/mongodb-thread-group.png)

#### 添加 MongoDB Source Config

选中刚才添加的 MongoDB Thread Group，点右键，在右键菜单选择菜单项：添加 -> 配置元件 -> MongoDB Source Config。配置面板同样在右侧窗格展示。参数配置如下：

- 名称：MongoDB Source Config
- Server Address List：127.0.0.1
- MongoDB Source：jmeterMongoDBSource

其他参数保持默认，如图：![MongoDB Source Config](/uploads/20160417/mongodb-source-config.png)

#### 添加　MongoDB Script

同样选中 MongoDB Thread Group，点右键，在右键菜单选择菜单项：添加 -> Sampler -> MongoDB Script。右侧窗格展示配置面板，参数配置如下：

- MongoDB Source：jmeterMongoDBSource，与　MongoDB Source Config　中配置的一致。
- Database Name：test
- Script：db.jmeter.insert({ "p1" : "0", "p2" : "00", "bd" : "HUAWEI", "p3" : "001", "lo" : "110.802298", "pcode" : "010110468", "nt" : "wifi", "imsi" : "-", "uuid" : "49d66ae4a91b679a78d08ea4d61b7861_1460615318980" })

其他参数保持默认，如图：![MongoDB Script](/uploads/20160417/mongodb-script.png)

#### 添加察看结果树和聚合报告

还是选中 MongoDB Thread Group，点右键，在右键菜单选择菜单项：添加 -> 监听器 -> 察看结果树 和 聚合报告。

点击“启动”按钮开始测试。结果如图：![测试结果](/uploads/20160417/mongodb-test-result.png)
