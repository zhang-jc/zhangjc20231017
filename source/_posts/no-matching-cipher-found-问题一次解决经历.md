title: no matching cipher found 问题一次解决经历
date: 2019-12-10 15:27:10
tags:
- CentOS
- Linux
categories:
- 操作系统
- Linux
---

本次问题解决纯属蒙对了，原理不清楚。

当我从一台 CentOS 7.3 的服务器通过 ssh 登录另外一台 CentOS 6.8 的服务器时出现以下错误信息：

    [root@192-168-72-75 .ssh]# ssh -p65522 bddev@192.168.72.208
    no matching cipher found: client arcfour server chacha20-poly1305@openssh.com,aes128-ctr,aes192-ctr,aes256-ctr,aes128-gcm@openssh.com,aes256-gcm@openssh.com

解决方法是在“192-168-72-75”服务器上修改 /etc/ssh/ssh_config 配置文件，添加以下内容：

    Host *
    	 GSSAPIAuthentication yes
         Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
         MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160

以上内容在原配置文件中本来就有，可能只是注释掉了，修改的时候仔细核对下。