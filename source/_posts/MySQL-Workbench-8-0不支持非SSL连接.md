---
title: MySQL Workbench 8.0不支持非SSL连接
date: 2022-01-27 11:19:30
tags:
- MySQL
categories:
- 数据库
- MySQL
---

连接错误信息：SSL connection error: SSL is required but the server doesn't support it。如下图：

![错误信息](/images/20220127/mysql-ssl-error1.jpg)

解决方法是在配置连接时“Advanced”选项卡中的Others中添加如下参数：

```Text
useSSL=0
```

如图：![禁用SSL](/images/20220127/mysql-ssl-error2.jpg)

测试连接出现警告信息，选择“Continue Anyway”：

![警告信息](/images/20220127/mysql-ssl-error3.jpg)

![警告信息](/images/20220127/mysql-ssl-error4.jpg)