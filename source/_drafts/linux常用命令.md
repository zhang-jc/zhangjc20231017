title: linux常用命令
tags:
---
**重启cron服务**

分为两种情况：

1. 系统中有 service 命令时：

	$ service crond start //启动服务  
	$ service crond stop //关闭服务  
	$ service crond restart //重启服务

2. 没有 service 命令时：

	/etc/init.d/cron stop  
	/etc/init.d/cron start

