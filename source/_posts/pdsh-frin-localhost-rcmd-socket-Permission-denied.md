---
title: 'pdsh@frin: localhost: rcmd: socket: Permission denied'
date: 2021-12-06 11:21:38
tags:
- Hadoop
- Ubuntu
categories:
- 大数据
- Hadoop
---

在部署单节点Hadoop集群时，执行sbin/start-dfs.sh出现以下错误：

```Shell
$ sbin/start-dfs.sh
Starting namenodes on [localhost]
pdsh@frin: localhost: rcmd: socket: Permission denied
Starting datanodes
pdsh@frin: localhost: rcmd: socket: Permission denied
Starting secondary namenodes [frin]
pdsh@frin: frin: rcmd: socket: Permission denied
```

解决方法：在/etc/pdsh/下创建rcmd_default文件，输入内容：ssh。