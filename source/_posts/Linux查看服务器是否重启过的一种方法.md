---
title: Linux查看服务器是否重启过的一种方法
date: 2022-03-09 17:27:06
tags:
- Linux
categories:
- Linux
---

使用 uptime 命令可以方便检查服务器是否发生了重启。uptime 命令手册中说：uptime会在一行中显示下列信息：当前时间、系统运行了多久时间、当前登录的用户有多少，以及前 1、5 和 15 分钟系统的平均负载。当然可以添加参数以不同的方式展示信息，如下：

```Shell
$ uptime
 17:30:22 up 23 min,  1 user,  load average: 0.55, 0.63, 0.46
$ uptime -p
up 25 minutes
$ uptime -s
2022-03-09 17:06:48
```