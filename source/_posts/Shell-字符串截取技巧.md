title: Shell 字符串截取技巧
date: 2020-11-26 15:55:22
tags:
- Shell
- Linux
categories:
- 语言
- Shell
---

- # 号截取：删除左边字符，保留右边字符。
- ## 号截取：删除左边字符，保留右边字符。
- %号截取：删除右边字符，保留左边字符
- %% 号截取：删除右边字符，保留左边字符

示例一：

	$ var='abcbd'
	$ echo ${var#*b}
	cbd
	$ echo ${var##*b}
	d
	$ echo ${var%b*}
	abc
	$ echo ${var%%b*}
	a
	
示例二：

	$ cat /etc/passwd
	root:x:0:0:root:/root:/bin/bash
	daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
	bin:x:2:2:bin:/bin:/usr/sbin/nologin
	sys:x:3:3:sys:/dev:/usr/sbin/nologin
	sync:x:4:65534:sync:/bin:/bin/sync
	$ for line in `cat /etc/passwd`; do echo ${line%%:*}; done
	root
	daemon
	bin
	sys
	sync
	