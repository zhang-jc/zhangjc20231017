title: CentOS7.7 安装 MySQL 5.7
date: 2020-11-26 17:19:38
tags:
- Linux
- CentOS
- MySQL
categories:
- 数据库
- MySQL
---

### 添加 MySQL Yum Repository

a. 从 MySQL Yum Repository 下载页面（https://dev.mysql.com/downloads/repo/yum/）选择对应的版本下载。

b. 使用以下命令安装 RPM 包：

	# sudo yum localinstall mysql80-community-release-el7-3.noarch.rpm
	
### 版本选择

a. 查看当前启用的版本：

	# yum repolist all | grep mysql
	mysql-cluster-7.5-community/x86_64 MySQL Cluster 7.5 Community      禁用
	mysql-cluster-7.5-community-source MySQL Cluster 7.5 Community - So 禁用
	mysql-cluster-7.6-community/x86_64 MySQL Cluster 7.6 Community      禁用
	mysql-cluster-7.6-community-source MySQL Cluster 7.6 Community - So 禁用
	mysql-cluster-8.0-community/x86_64 MySQL Cluster 8.0 Community      禁用
	mysql-cluster-8.0-community-source MySQL Cluster 8.0 Community - So 禁用
	mysql-connectors-community/x86_64  MySQL Connectors Community       启用:    175
	mysql-connectors-community-source  MySQL Connectors Community - Sou 禁用
	mysql-tools-community/x86_64       MySQL Tools Community            启用:    120
	mysql-tools-community-source       MySQL Tools Community - Source   禁用
	mysql-tools-preview/x86_64         MySQL Tools Preview              禁用
	mysql-tools-preview-source         MySQL Tools Preview - Source     禁用
	mysql55-community/x86_64           MySQL 5.5 Community Server       禁用
	mysql55-community-source           MySQL 5.5 Community Server - Sou 禁用
	mysql56-community/x86_64           MySQL 5.6 Community Server       禁用
	mysql56-community-source           MySQL 5.6 Community Server - Sou 禁用
	mysql57-community/x86_64           MySQL 5.7 Community Server       禁用
	mysql57-community-source           MySQL 5.7 Community Server - Sou 禁用
	mysql80-community/x86_64           MySQL 8.0 Community Server       启用:    211
	mysql80-community-source           MySQL 8.0 Community Server - Sou 禁用
	
b. 启用 MySQL5.7 版本

	# sudo yum-config-manager --disable mysql80-community
	# sudo yum-config-manager --enable mysql57-community
	
### 安装 MySQL5.7

	# sudo yum install mysql-community-server
	
### 启动 MySQL Server

	# sudo service mysqld start
	
检查 MySQL 启动状态

	# sudo service mysqld status