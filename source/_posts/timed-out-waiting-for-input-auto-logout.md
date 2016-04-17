title: 'timed out waiting for input: auto-logout'
tags:
  - Linux
categories:
  - 操作系统
  - Linux
date: 2015-11-26 09:39:12
---

发生这个的原因是系统设置了等待用户输入 time out （超时）参数。登陆系统，执行以下命令可以查看参数值：

	[root@vm-10-176-30-167 ~]# echo $TMOUT
	120

当前参数是 120 秒，所以很快就超时自动退出。解决方法是在 /etc/profile 中修改该参数的值：

	[root@vm-10-176-30-167 ~]# vi /etc/profile

在 /etc/profile 末尾增加一下配置，将超时时间改为 10 小时。
	
	TMOUT=36000
	export TMOUT

保存，退出系统并重新登录，或者执行以下命令生效新配置参数：

	[root@vm-10-176-30-167 ~]# source /etc/profile
	[root@vm-10-176-30-167 ~]# echo $TMOUT
	36000
