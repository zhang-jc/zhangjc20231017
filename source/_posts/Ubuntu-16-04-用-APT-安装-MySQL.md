title: Ubuntu 16.04 用 APT 安装 MySQL
tags:
  - Ubuntu
  - MySQL
categories:
  - 数据库
  - MySQL
date: 2017-06-11 18:39:03
---

### 安装 MySQL

用以下命令安装 MySQL:

    sudo apt-get install mysql-server
    
这个命令会安装 MySQL 服务器、客户端和公共文件。安装过程会出现两个要求输入的对话框：

- 输入 MySQL root 用户的密码。
- 指明是否创建 test 数据库。

<!-- more -->

### 启动/停止 MySQL

安装之后，MySQL 服务器会自动启动。用下面的命令检查 MySQL 服务器状态：

    $ sudo service mysql status
    ● mysql.service - MySQL Community Server
       Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
       Active: active (running) since 日 2017-06-11 17:05:11 CST; 14min ago
     Main PID: 11970 (mysqld)
       CGroup: /system.slice/mysql.service
               └─11970 /usr/sbin/mysqld
    
    6月 11 17:05:09 frin systemd[1]: Starting MySQL Community Server...
    6月 11 17:05:11 frin systemd[1]: Started MySQL Community Server.

使用下面的命令停止 MySQL：

    sudo service mysql stop
    
使用下面的命令启动 MySQL：

    sudo service mysql start
    
### 连接/断开 MySQL 服务器：

使用 root 用户连接 MySQL 服务器：

    $ mysql -h 127.0.0.1 -u root -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 4
    Server version: 5.7.18-0ubuntu0.16.04.1 (Ubuntu)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    
使用以下命令断开连接：

    mysql> quit
    Bye

### 创建数据库

创建 Hive 元数据使用的数据库 hive。使用以下命令：

    mysql> CREATE DATABASE hive;
    Query OK, 1 row affected (0.03 sec)
    
使用以下命令切换到 hive 数据库：

    mysql> use hive;
    Database changed

使用以下命令创建用户 hive，并授权：

    mysql> use mysql;
    Database changed

    mysql> CREATE USER 'hive'@'%' IDENTIFIED BY 'hive@mysql123';
    Query OK, 0 rows affected (0.04 sec)
    
    mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON hive.* TO 'hive'@'%';
    Query OK, 0 rows affected (0.01 sec)
    
    mysql> CREATE USER 'hive'@'localhost' IDENTIFIED BY 'hive@mysql123';
    Query OK, 0 rows affected (0.12 sec)
    
    mysql> GRANT ALL ON hive.* TO 'hive'@'localhost';
    Query OK, 0 rows affected (0.01 sec)
    
    mysql> FLUSH PRIVILEGES;
    Query OK, 0 rows affected (0.00 sec)
