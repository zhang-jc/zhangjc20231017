title: Ubuntu 15.10 安装 MongoDB
tags:
  - MongoDB
  - Ubuntu
categories:
  - 数据库
  - MongoDB
date: 2016-04-16 11:47:22
---

本篇主要参考官方安装文档，在 Ubuntu 15.10 上安装并启动。中间遇到一些问题，已经找到解决方案。

<!-- more -->

### 概述

从 3.2 版本开始，MongoDB 不再支持 32 位的平台。

使用本教程从 **.deb** 包在 LTS Ubuntu Linux 系统上安装 MongoDB 社区版。虽然 Ubuntu 包含自己的 MongoDB 包，官方 MongoDB 社区版包通常是更新的。

### 安装包

MongoDB 在自己的仓库中提供了官方支持的安装包。这个仓库包含一下包：

- mongodb-org：一个**元安装包**，可以自动安装下面的四个组件安装包。
- mongodb-org-server：包含 [mongod](https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod) 守护进程和相关的配置及初始脚本。
- mongodb-org-mongos：包含 [mongos](https://docs.mongodb.org/manual/reference/program/mongos/#bin.mongos) 守护进程。
- mongodb-org-shell：包含 [mongo](https://docs.mongodb.org/manual/reference/program/mongo/#bin.mongo) shell。
- mongodb-org-tools：包含以下 MongoDB 工具：[mongoimport](https://docs.mongodb.org/manual/reference/program/mongoimport/#bin.mongoimport)、[bsondump](https://docs.mongodb.org/manual/reference/program/bsondump/#bin.bsondump)、[mongodump](https://docs.mongodb.org/manual/reference/program/mongodump/#bin.mongodump)、[mongoexport](https://docs.mongodb.org/manual/reference/program/mongoexport/#bin.mongoexport)、[mongofiles](https://docs.mongodb.org/manual/reference/program/mongofiles/#bin.mongofiles)、[mongooplog](https://docs.mongodb.org/manual/reference/program/mongooplog/#bin.mongooplog)、[mongoperf](https://docs.mongodb.org/manual/reference/program/mongoperf/#bin.mongoperf)、[mongorestore](https://docs.mongodb.org/manual/reference/program/mongorestore/#bin.mongorestore)、[mongostat](https://docs.mongodb.org/manual/reference/program/mongostat/#bin.mongostat) 和 [mongotop](https://docs.mongodb.org/manual/reference/program/mongotop/#bin.mongotop)。

这些包与 Ubuntu 提供的 **mongodb**、**mongodb-server** 和 **mongodb-clients** 包冲突。

安装包提供的默认的 **/etc/mongod.conf** 配置文件默认设置 **bind_ip** 为 127.0.0.1。在初始化一个 [replica set](https://docs.mongodb.org/manual/reference/glossary/#term-replica-set) 之前根据环境的需要修改这个设置。

### 初始化脚本

**mongodb-org** 安装包包含许多[初始化脚本](https://docs.mongodb.org/manual/reference/glossary/#term-init-script)，包含初始化脚本 **/etc/init.d/mongod**。你可以用这些脚本停止、启动和重启守护进程。

安装包使用 **/etc/mongod.conf** 文件结合初始化脚本配置 MongoDB。查看 [配置文件](https://docs.mongodb.org/manual/reference/configuration-options/) 参考获取配置文件中的设置参数。

从 3.2.5 版本开始，不再有 [mongos](https://docs.mongodb.org/manual/reference/program/mongos/#bin.mongos) 初始脚本。[mongos](https://docs.mongodb.org/manual/reference/program/mongos/#bin.mongos) 进程只在 [sharding](https://docs.mongodb.org/manual/core/sharding/) 时用。你可以用 **mongod** 初始脚本生成自己的 [mongos](https://docs.mongodb.org/manual/reference/program/mongos/#bin.mongos) 初始化脚本在这样的环境使用。查看 [mongos](https://docs.mongodb.org/manual/reference/program/mongos/#bin.mongos) 参考获取详细配置。

### 安装 MongoDB 社区版

MongoDB 只为 64 位长期支持的 Ubuntu 发行版提供安装包。当前，这意味着 12.04 LTS (Precise Pangolin) 和 14.04 LTS (Trusty Tahr) 两个版本。同时b安装包在其他 Ubuntu 发行版本也可以运行，这不是一个维持的配置。

#### 1、导入安装包管理系统使用的公钥

Ubuntu 包管理工具（例如 **dpkg** 和 **apt**) 通过要求发布者签名的 GPG 密钥确保安装包的一致和真实。输入下面的命令导入 [MongoDB GPG 公钥](https://www.mongodb.org/static/pgp/server-3.2.asc?_ga=1.177583341.957577678.1460369205)：

	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927

#### 2、为 MongoDB 创建一个文件列表

使用适用你的 Ubuntu 版本的命令创建 **/etc/apt/sources.list.d/mongodb-org-3.2.list** 列表文件：

  Ubuntu 12.04

	echo "deb http://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

  Ubuntu 14.04

	echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

#### 3、重新加载本地安装包数据库

输入以下命令重新加载本地安装包数据库：

	sudo apt-get update

重新加载过程中可能会报以下错误信息，多试两次即可：

	错误 http://repo.mongodb.org trusty/mongodb-org/3.2/multiverse Translation-zh_CN                                                              
	  不能连接到 repo.mongodb.org：http：
	错误 http://repo.mongodb.org trusty/mongodb-org/3.2/multiverse Translation-zh
	  不能连接到 repo.mongodb.org：http：
	错误 http://repo.mongodb.org trusty/mongodb-org/3.2/multiverse Translation-en_US
	  不能连接到 repo.mongodb.org：http：
	错误 http://repo.mongodb.org trusty/mongodb-org/3.2/multiverse Translation-en
	  不能连接到 repo.mongodb.org：http：
	  
如果报以下错误信息，可以将添加到文件列表中的信息由 HTTP 改为 HTTPS：

  错误信息：

	W: 无法下载 http://repo.mongodb.org/apt/ubuntu/dists/trusty/mongodb-org/3.2/multiverse/binary-amd64/Packages  Hash 校验和不符

  第 2 步创建文件列表命令改为：

	echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

再次执行更新命令成功。

#### 4、安装 MongoDB

可以安装 MongoDB 最新稳定版本或者指定的版本。

#### 5、安装 MongoDB 最新稳定版本

输入下面的命令：

	sudo apt-get install -y mongodb-org
	
#### 6、安装特定版本的 MongoDB

安装特定发行版，你必须分别单独用版本号指定每个组件，像下面示例：

	sudo apt-get install -y mongodb-org=3.2.5 mongodb-org-server=3.2.5 mongodb-org-shell=3.2.5 mongodb-org-mongos=3.2.5 mongodb-org-tools=3.2.5

如果你只安装 **mongodb-org=3.2.5**，并且没有包含组件安装包，MongoDB 每个最新的版本将被安装，不管你指明了哪个版本。

#### 7、固定一个特定版本的 MongoDB

虽然你可以指定任何可以获取的 MongoDB 版本，当新版本可以获取时 **apt-get** 将更新安装包。为了防止无意的更新，固定安装包。为了固定当前安装的 MongoDB 版本，输入以下命令序列：

	echo "mongodb-org hold" | sudo dpkg --set-selections
	echo "mongodb-org-server hold" | sudo dpkg --set-selections
	echo "mongodb-org-shell hold" | sudo dpkg --set-selections
	echo "mongodb-org-mongos hold" | sudo dpkg --set-selections
	echo "mongodb-org-tools hold" | sudo dpkg --set-selections

### 运行 MongoDB 社区版

默认的 MongoDB 实例在 **/var/lib/mongodb** 存储数据文件，并且在 **/var/log/mongodb** 存储日志文件，并且用 **mongodb** 用户用户帐号运行。你可以在 **/etc/mongod.conf** 中指定替换的日志和数据文件目录。查看 [systemLog.path](https://docs.mongodb.org/manual/reference/configuration-options/#systemLog.path) 和 [storage.dbPath](https://docs.mongodb.org/manual/reference/configuration-options/#storage.dbPath) 获取额外的信息。

如果改变运行 MongoDB 进程的用户，你**必须**修改 **/var/lib/mongodb** 和 **/var/log/mongodb** 目录的访问控制权限给这个用户对这些目录的访问权限。

#### 1、启动 MongoDB

输入下面的命令启动 [mongod](https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod)：

	sudo service mongod start

执行上面的命令会报错，错误信息如下：

	Failed to start mongod.service: Unit mongod.service failed to load: No such file or directory.

原因是缺少 systemd 的 service 文件。不需要重新开始安装或者换用其他仓库。创建文件 /lib/systemd/system/mongod.service，并输入以下内容：

	[Unit]
	Description=High-performance, schema-free document-oriented database
	After=network.target
	
	[Service]
	User=mongodb
	ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf
	
	[Install]
	WantedBy=multi-user.target

> 注意：/lib/systemd/system/mongod.service 和配置内容中的文件名可能需要对应的做修改，可能是 mongodb.service 和 mongodb.conf。参考网上的资料是 mongodb，而我的系统安装后都是 mongod。

重新执行启动命令，提示以下信息，检查 mongo 进程不存在，说明未启动成功：

	Warning: mongod.service changed on disk. Run 'systemctl daemon-reload' to reload units.

根据提示执行以下命令，并输入用户密码，再次执行启动命令，这次启动成功 :)

	systemctl daemon-reload

#### 2、确认 MongoDB 已经成功启动

通过读取检查 **/var/log/mongodb/mongod.log** 日志文件中的下面一行记录确认 [mongod](https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod) 进程已经成功启动。

	[initandlisten] waiting for connections on port <port>

**<port>** 是在 **/etc/mongod.conf** 文件中配置的端口号，默认是 27017。

以下是我的安装过程的启动日志信息：

![mongodb-start-log](/uploads/20160416/mongodb_start_log.png)

#### 3、停止 MongoDB

如果需要，你可以输入以下命令停止 [mongod](https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod) 进程：

	sudo service mongod stop

#### 4、重启 MongoDB

输入以下命令重启 [mongod](https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod) 进程：

	sudo service mongod restart

### 卸载 MongoDB 社区版

要从系统中完全移除 MongoDB，你必须移除 MongoDB 应用程序、配置文件和所有包含数据和日志的目录。下面的章节指导你通过必要的步骤完成卸载。

> 警告：
> 这个过程将完全*移除* MongoDB、配置和*所有*数据库。这个过程是不能恢复的，因此确认执行该过程前确保你的配置和数据应经备份。

#### 1、停止 MongoDB

输入以下命令停止 [mongod](https://docs.mongodb.org/manual/reference/program/mongod/#bin.mongod) 进程：

	sudo service mongod stop

#### 2、移除安装包

移除所有以前安装的 MongoDB 安装包。

	sudo apt-get purge mongodb-org*

#### 3、移除数据目录

移除 MongoDB 数据库和日志文件。

	sudo rm -r /var/log/mongodb
	sudo rm -r /var/lib/mongodb
