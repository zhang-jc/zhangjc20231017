title: shell -c
date: 2020-12-03 10:57:48
tags:
- Linux
- Shell
categories:
- 操作系统
- Linux
---

shell -c {string}：表示命令从-c后的字符串读取。在需要使用管道或者重定向需要sudo时很有用，如下：

	$ sudo find ../*/exportFiles -mtime +15 -name "*" | xargs -I {} rm -rf {}
	rm: cannot remove `i_20201108_CONTENTINFO_xy_001.dat': Permission denied
	
按照以下方式处理即可：

	$ sudo sh -c 'find ../*/exportFiles -mtime +15 -name "*" | xargs -I {} rm -rf {}'