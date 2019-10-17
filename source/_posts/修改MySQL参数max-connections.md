title: 修改MySQL参数max_connections
date: 2019-10-17 11:38:51
tags:
- MySQL
categories:
- 数据库
- MySQL
---
编辑配置文件 /etc/my.cnf，修改以下参数：

    max_connections=2000

MySQL 管理员登陆 MySQL 数据库，在命令行执行：

    mysql> set GLOBAL max_connections=3000;
    Query OK, 0 rows affected (0.01 sec)
    mysql> show variables like '%connections%';
    +----------------------+-------+
    | Variable_name        | Value |
    +----------------------+-------+
    | max_connections      | 3000  |
    | max_user_connections | 0     |
    +----------------------+-------+
    2 rows in set (0.00 sec)