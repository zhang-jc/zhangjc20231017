title: Rsyslog omhttp（HTTP 输出模块）
tags:
  - Rsyslog
categories:
  - 大数据
  - Rsyslog
date: 2019-04-01 15:31:48
---

使用 Rsyslog 的 omhttp 模块可以将收集的日志数据以 HTTP 请求的方式输出。该模块支持单条/批量发送数据，支持 GZIP 压缩，支持 HTTPS。

<!--more-->

Rsyslog 默认未包含 omhttp 模块，需要重新编译。编译过程参考我的博文：
[CentOS 7.3 编译 Rsyslog 8.1903.0](http://zhang-jc.github.io/2019/04/03/CentOS-7-3-%E7%BC%96%E8%AF%91-Rsyslog-8-1903-0/)
[CentOS 6.5 编译 Rsyslog 8.1903.0](http://zhang-jc.github.io/2019/04/03/CentOS-6-5-%E7%BC%96%E8%AF%91-Rsyslog-8-1903-0/)

CentOS 6.5 系统下编译比较麻烦，可以下载我编译后的 so 文件：[CentOS 6.5 编译 omhttp.so 下载](http://zhang-jc.github.io/2019/04/06/CentOS-6-5-%E7%BC%96%E8%AF%91-omhttp-so-%E4%B8%8B%E8%BD%BD/)

一个简单的配置示例如下：

    module(load="omhttp")
    template(name="tpl1" type="string" string="{\"type\":\"syslog\", \"host\":\"%HOSTNAME%\"}")
    action(
        type="omhttp"
        server="127.0.0.1"
        serverport="8080"
        restpath="events"
        template="tpl1"
        action.resumeRetryCount="3"
    )