title: 通过 MongoDB 日志信息定位操作来源
tags:
  - MongoDB
categories:
  - 数据库
  - MongoDB
date: 2016-05-30 23:18:10
---

### 起因

一天中午突然发现生产系统使用的 MongoDB 库被删掉了。幸亏有备份，很快恢复，生产系统并未受很大影响。问了团队成员，都未进行过删除操作，说明可能是两种情况：一、误删，连操作者自己也没意识到做了一个删除操作；二、bug，在某个程序中存在一个 bug。

### local.oplog.rs

MongoDB 的复制集是通过 oplog 来实现的，主库的更改操作会被记录到主库的 oplog 日志中，然后从库通过异步方式复制主库的 oplog 文件并且将 oplog 日志应用到从库，从而实现了与主库的同步。

先查看 oplog 看是否有删除操作。查过之后，并未发现有 remove 的操作。

oplog 说明：

    > db.oplog.rs.findOne()
    {
      "ts" : Timestamp(1419392438, 1),
      "h" : NumberLong("3445526183368758260"),
      "v" : 2,
      "op" : "n",
      "ns" : "",
      "o" : {
        "msg" : "Reconfig set",
        "version" : 3
      }
    }

- ts：8字节的时间戳，由4字节unix timestamp + 4字节自增计数表示。这个值很重要，在选举(如master宕机时)新primary时，会选择ts最大的那个secondary作为新primary。
- op：1字节的操作类型，例如：
  - "i"： insert
  - "u"： update
  - "d"： delete
  - "c"： db cmd
  - "db"：声明当前数据库 (其中ns 被设置成为=>数据库名称+ '.')
  - "n":  no op,即空操作，其会定期执行以确保时效性
- ns：操作所在的namespace。
- o：操作所对应的document，即当前操作的内容（比如更新操作时要更新的的字段和值）
- o2: 在执行更新操作时的where条件，仅限于update时才有该属性。

### MongoDB 系统日志

MongoDB 系统日志路径从配置文件中可以查看。MongoDB 进程信息中可以看到配置文件的位置：

    [root@10-180-86-57 ~]# ps -ef|grep mongo
    root      1564     1  1  2015 ?        2-21:32:34 /usr/local/mongodb/bin/mongod -f /usr/local/mongodb/mongodb.conf

日志文件路径配置如下：

    [root@10-180-86-57 ~]# more /usr/local/mongodb/mongodb.conf
    systemLog:
      destination: file
      path: "/data/mongodb/log/mongodb.log"
      logAppend: true

查看日志信息发现有 dropDatabase 的操作：

    [root@10-180-86-57 log]# cat mongodb.log |grep dropDatabase
    2016-05-24T11:24:37.994+0800 [repl writer worker 1] dropDatabase md starting
    2016-05-24T11:24:38.357+0800 [repl writer worker 1] dropDatabase md finished

md 正是被删除的库。时间范围已经明确，剩下的就是找到删除操作的来源，通过时间查看该时间前有哪些 IP 连接了数据库：

    [root@10-180-86-57 log]# cat mongodb.log |grep "2016-05-24T11:24"
    2016-05-24T11:24:34.960+0800 [initandlisten] connection accepted from 10.57.172.146:58082 #5171827 (55 connections now open)
    2016-05-24T11:24:35.011+0800 [conn5171827]  authenticate db: admin { authenticate: 1, user: "bigdata", nonce: "xxx", key: "xxx" }
    2016-05-24T11:24:36.483+0800 [conn5171600] end connection 10.149.13.17:4202 (54 connections now open)
    2016-05-24T11:24:37.994+0800 [repl writer worker 1] dropDatabase md starting
    2016-05-24T11:24:38.137+0800 [conn5171338] end connection 10.149.13.6:32360 (53 connections now open)
    2016-05-24T11:24:38.252+0800 [repl writer worker 1] removeJournalFiles
    2016-05-24T11:24:38.357+0800 [repl writer worker 1] dropDatabase md finished

发现在这个时间有一个内容 IP（10.57.172.146） 有连接数据库的操作。虽然问题没有重新，但基本可能定位为误删操作。review 同事代码确定没有问题后，问题没有再现。

### 解决方案
#### 读写权限分离

这个库下存放的都是配置信息，变动很少，但读取很频繁，而且多个地方都会读取。为了控制权限，创建这个库的只读账号，对外只开放只读账号，只有管理功能使用可写账号。

#### 备份

这次事故就是因为有备份才可以快速恢复的。
