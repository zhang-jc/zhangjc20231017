title: 'Hiveserver2 User: ** is not allowed to impersonate **'
date: 2020-09-09 11:00:06
tags:
- Hadoop
- Hive
- HDFS
categories:
- 大数据
- Hive
---

确认已经配置了Hadoop的超级代理用户，并且也重启了NameNode服务。后来发现是因为ResourceManager没有重启，任务提交到Yarn时出现权限不够导致的。

使用以下刷新命令，不必重启：

    $ hdfs dfsadmin -refreshSuperUserGroupsConfiguration
    $ yarn rmadmin -refreshSuperUserGroupsConfiguration

执行完成后需要重启HiveServer2。然后问题解决。
