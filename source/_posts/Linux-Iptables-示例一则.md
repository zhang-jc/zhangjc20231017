title: Linux Iptables 示例一则
date: 2020-11-30 14:06:24
tags:
- Linux
- 防火墙
- Iptables
categories:
- 操作系统
- Linux
---

关于 Iptables 的介绍个人强烈推荐：http://www.zsythink.net/archives/tag/iptables/page/2/。这位兄弟介绍的很详细。

我个人的需求是在同一个网络内从网络上把测试主机与正式环境主机间的网络进行隔离。我的思路是采用 OUTPUT filter 表，采用黑名单方式禁用正式环境网络的访问。如果确实需要访问正式环境的某个IP和端口，则添加特殊规则放行。根据 Iptables 的规则匹配顺序原则，放行特定IP和端口的规则要在网段禁用规则之前。以下是我的名列列表：

	iptables -t filter -P OUTPUT ACCEPT
	iptables -F OUTPUT
	iptables -t filter -I OUTPUT -d 192.168.72.0/24 -j DROP
	iptables -t filter -I OUTPUT -d 192.168.72.1/32 -j ACCEPT
	service iptables save
	
1. 第一条命令是修改OUTPUT链的默认策略为ACCEPT，这样做的优点是方便通过yum安装软件时不需要设置一堆放行规则，当然网络入口的防火墙需要做好安全防护。
2. 第二条命令是情况OUTPUT链的所有规则，重新进行配置。
3. 第三条命令是插入一条filter规则，禁用当前服务器对192.168.72网段的访问。
4. 第四条命令是在filter表插入放行规则，允许当前服务器访问网关192.168.72.1。

注意，因为filter规则添加采用的是 -I 插入模式，每次都是在表的最前名插入，所有第三、四的顺序不能颠倒。如果采用的是 -A 追加模式，则第三、四命令需要颠倒。
