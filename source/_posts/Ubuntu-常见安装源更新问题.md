title: Ubuntu 常见安装源更新问题
tags:
  - Ubuntu
  - 操作系统
categories:
  - 操作系统
  - Ubuntu
date: 2016-04-16 20:19:29
---


以下两种情况都是因为无效安装源导致的，可以直接删除无效安装源解决。

<!-- more -->

有两个位置可以添加仓库。/etc/apt/sources.list 文件和所有在 /etc/apt/sources.list.d/ 目录下以 .list 结尾的文件。所以，只需要检查这个目录下的文件并移除不需要的即可。

**1、Duplicate sources.list entry**

	W: Duplicate sources.list entry http://archive.ubuntukylin.com:10006/ubuntukylin/ trusty/main amd64 Packages (/var/lib/apt/lists/archive.ubuntukylin.com:10006_ubuntukylin_dists_trusty_main_binary-amd64_Packages)
	W: Duplicate sources.list entry http://archive.ubuntukylin.com:10006/ubuntukylin/ trusty/main i386 Packages (/var/lib/apt/lists/archive.ubuntukylin.com:10006_ubuntukylin_dists_trusty_main_binary-i386_Packages)
	W: 您可能需要运行 apt-get update 来解决这些问题

通过以下命令查找目标文件：

	#cd /etc/apt/sources.list.d && find . -name '*.list*'|xargs grep "archive.ubuntukylin.com:10006"
	./wps-office.list.save:deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main
	./sogoupinyin.list:deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main
	./sogoupinyin.list.save:deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main
	./wps-office.list:deb http://archive.ubuntukylin.com:10006/ubuntukylin trusty main

删除对应的文件：

	#sudo rm -f wps-office.list*
	#sudo rm -f sogoupinyin.list*

再次执行安装源更新命令，问题解决：

	sudo apt-get update

**2、404  Not Found**

还有一种常见问题是找不到的错误，如下：

	错误 http://ppa.launchpad.net wily/main amd64 Packages
	  404  Not Found
	错误 http://ppa.launchpad.net wily/main i386 Packages
	  404  Not Found
	忽略 http://ppa.launchpad.net wily/main Translation-en_US
	忽略 http://ppa.launchpad.net wily/main Translation-zh_CN
	忽略 http://ppa.launchpad.net wily/main Translation-en
	忽略 http://ppa.launchpad.net wily/main Translation-zh
	下载 1,525 kB，耗时 43秒 (35.3 kB/s)
	W: 无法下载 http://ppa.launchpad.net/tldm217/tahutek.net/ubuntu/dists/wily/main/binary-amd64/Packages  404  Not Found
	W: 无法下载 http://ppa.launchpad.net/tldm217/tahutek.net/ubuntu/dists/wily/main/binary-i386/Packages  404  Not Found
	E: 部分索引文件下载失败。如果忽略它们，那将转而使用旧的索引文件。

删除来源自 http://ppa.launchpad.net 的安装源列表，重新更新：

	#ls -l /etc/apt/sources.list.d/tldm217-ubuntu-tahutek_net-wily.list*
	-rw-r--r-- 1 root root 138  4月 16 02:03 /etc/apt/sources.list.d/tldm217-ubuntu-tahutek_net-wily.list
	-rw-r--r-- 1 root root 138  4月 16 02:03 /etc/apt/sources.list.d/tldm217-ubuntu-tahutek_net-wily.list.save
	
	#sudo rm -f /etc/apt/sources.list.d/tldm217-ubuntu-tahutek_net-wily.list*
	
再次执行更新，问题解决。
