---
title: Redis编译安装过程问题
date: 2022-02-24 10:56:24
tags:
- Redis
categories:
- 数据库
- Redis
---

# jemalloc/jemalloc.h

```Text
zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: No such file or directory
 #include <jemalloc/jemalloc.h>
                               ^
compilation terminated.
make[1]: *** [adlist.o] Error 1
make[1]: Leaving directory `/home/redis/redis-stable/src'
make: *** [all] Error 2
```

执行以下命令：

```Shell
make distclean
make
```

# tcl 8.5

```Text
You need tcl 8.5 or newer in order to run the Redis test
make[1]: *** [test] Error 1
make[1]: Leaving directory `/home/redis/redis-stable/src'
make: *** [test] Error 2
```

下载[Active State](https://www.activestate.com/)。此处下载二进制包“ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz”。安装过程：

```Shell
tar xzvf ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz
mv ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327 ActiveTcl-8.6
cd ActiveTcl-8.6
./install.sh
```

根据提示输入完成安装过程。将Tcl8.6加入Path中，将以下内容添加至/etc/profile.d/tcl.sh：

```Shell
export TCL_HOME=/opt/ActiveTcl-8.6
export PATH=$TCL_HOME/bin:$PATH
```

执行以下命令加载环境变量：

```Shell
source /etc/profile
```

安装完成后，在我的环境里面仍然提示上面的错误。查看runtest脚本源码，并使用which命令验证才发现是我的Docker环境中没有安装which命令。-_-||

所以，一言不合就撸源代码是一个好习惯(*￣︶￣)
