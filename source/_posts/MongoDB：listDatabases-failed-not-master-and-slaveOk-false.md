title: 'MongoDB：listDatabases failed : not master and slaveOk=false'
tags:
  - MongoDB
categories:
  - 数据库
  - MongoDB
date: 2018-09-27 20:14:24
---

#### 异常描述

如果在 MongoDB 的 SECONDARY 上查询数据时会报如下错误信息：

    > show databases;
    2018-09-20T17:40:55.377+0800 E QUERY [thread1] Error: listDatabases failed:{ "ok" : 0, "errmsg" : "not master and slaveOk=false", "code" : 13435 } :
    _getErrorWithCode@src/mongo/shell/utils.js:25:13
    Mongo.prototype.getDBs@src/mongo/shell/mongo.js:62:1
    shellHelper.show@src/mongo/shell/utils.js:781:19
    shellHelper@src/mongo/shell/utils.js:671:15
    @(shellhelp2):1:1

#### Mongo Shell 设置

如果在 Mongo Shell 中可以通过下面的命令允许从 SECONDARY 上查询数据：

    > rs.slaveOk();
    
#### JDBC 设置

示例代码如下：

    MongoClientOptions opts = (new MongoClientOptions.Builder().readPreference(ReadPreference.secondary())).build();
	MongoClient mongoClient = new MongoClient(${host}, opts);
