title: CentOS 重装 MongoDB
tags:
  - MongoDB
categories:
  - 数据库
  - MongoDB
date: 2017-09-15 11:46:13
---


服务器原来安装的 MongoDB 版本过久，在连接 MongoDB 3.4 后执行 show collections 命令无结果显示，所以需要重新安装 MongoDB 3.4。

原安装版本查看及卸载：

    # rpm -qa | grep mongodb
    mongodb-2.4.14-4.el6.x86_64
    # sudo yum erase $(rpm -qa | grep mongodb)
    
卸载完毕之后，按照 [MongoDB 官网手册](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)重新安装。
