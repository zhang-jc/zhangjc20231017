title: CentOS 删除 libc.so.6 后命令不可用故障恢复方法
date: 2019-04-04 16:40:32
tags:
- CentOS
categories:
- 操作系统
- Linux
---
在升级 glibc 的过程中需要先删除软连接 /lib64/libc.so.6。删除后执行系统命令会一直报错：

<!--more-->

    # ls -l libc.so.6
    lrwxrwxrwx 1 root root 12 4月   2 13:49 libc.so.6 -> libc-2.12.so
    # mv libc.so.6 libc.so.6.bak
    # mv libc.so.6.bak libc.so.6
    mv: error while loading shared libraries: libc.so.6: cannot open shared object file: No such file or directory

从上面的错误信息可以发现想通过 mv 命令恢复是行不通的。可以通过执行以下命令恢复：

    # LD_PRELOAD=/lib64/libc-2.12.so ln -s /lib64/libc-2.12.so /lib64/libc.so.6

如果软链到自己安装的 glibc 版本，通过以下命令：

    # LD_PRELOAD=/opt/glibc-2.14/lib/libc-2.14.so ln -s /opt/glibc-2.14/lib/libc-2.14.so /lib64/libc.so.6

如果上面的方法还是不行，可以使用下面的命令：

    # sln /lib64/libc-2.12.so /lib64/libc.so.6