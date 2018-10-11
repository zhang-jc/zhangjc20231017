title: MySQL 修改 max_allowed_packet
tags:
  - MySQL
categories:
  - 数据库
  - MySQL
date: 2018-10-12 01:25:22
---


通过 global 参数设置：

    set global max_allowed_packet = 32*1024*1024;

注意，下面的写法是不正确的：

    set global max_allowed_packet = 32m;

如果要重启也生效的话，在 my.cnf 中添加如下配置：

    max_allowed_packet=32m
