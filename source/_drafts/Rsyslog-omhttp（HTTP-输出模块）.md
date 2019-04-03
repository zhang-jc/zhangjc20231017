title: Rsyslog omhttp（HTTP 输出模块）
date: 2019-04-01 15:31:48
tags:
- Rsyslog
categories:
- 大数据
- Rsyslog
---
使用 Rsyslog 的 omhttp 模块可以将收集的日志数据以 HTTP 请求的方式输出。该模块支持单条/批量发送数据，支持 GZIP 压缩，支持 HTTPS。


[root@localhost rsyslog.d]# service rsyslog restart
Shutting down system logger:                               [  OK  ]
Starting system logger: rsyslogd: could not load module 'omhttp', errors: trying to load module /lib64/rsyslog/omhttp.so: /lib64/rsyslog/omhttp.so: cannot open shared object file: No such file or directory [v8.1903.0 try https://www.rsyslog.com/e/2066 ]
rsyslogd: module name 'omhttp' is unknown [v8.1903.0 try https://www.rsyslog.com/e/2209 ]
rsyslogd: error during parsing file /etc/rsyslog.d/rsyslog_nginx_kafka_cluster.conf, on or before line 15: errors occured in file '/etc/rsyslog.d/rsyslog_nginx_kafka_cluster.conf' around line 15 [v8.1903.0 try https://www.rsyslog.com/e/2207 ]
rsyslogd: error during parsing file /etc/rsyslog.conf, on or before line 55: warnings occured in file '/etc/rsyslog.conf' around line 55 [v8.1903.0 try https://www.rsyslog.com/e/2207 ]
                                                           [  OK  ]

# autoreconf -fvi
autoreconf: Entering directory `.'
autoreconf: configure.ac: not using Gettext
autoreconf: running: aclocal --force -I m4
autoreconf: configure.ac: tracing
autoreconf: configure.ac: not using Libtool
autoreconf: running: /usr/bin/autoconf --force
configure.ac:46: error: possibly undefined macro: AC_DISABLE_STATIC
      If this token and others are legitimate, please use m4_pattern_allow.
      See the Autoconf documentation.
configure.ac:49: error: possibly undefined macro: AC_LIBTOOL_DLOPEN
configure.ac:52: error: possibly undefined macro: AC_PROG_LIBTOOL
autoreconf: /usr/bin/autoconf failed with exit status: 1



checking for LIBESTR... configure: error: Package requirements (libestr >= 0.1.9) were not met:

No package 'libestr' found

# yum install libestr-devel


checking for LIBFASTJSON... configure: error: Package requirements (libfastjson >= 0.99.8) were not met:

No package 'libfastjson' found

# yum install libfastjson-devel
已加载插件：fastestmirror, priorities, security
设置安装进程
Loading mirror speeds from cached hostfile
 * base: mirrors.njupt.edu.cn
 * epel: mirrors.yun-idc.com
 * extras: mirrors.163.com
 * updates: centos.ustc.edu.cn
解决依赖关系
--> 执行事务检查
---> Package libfastjson-devel.x86_64 0:0.99.2-1.el6 will be 安装
--> 处理依赖关系 libfastjson = 0.99.2-1.el6，它被软件包 libfastjson-devel-0.99.2-1.el6.x86_64 需要
Package libfastjson-0.99.2-1.el6.x86_64 is obsoleted by libfastjson4-0.99.8-1.el6.x86_64 which is already installed
--> 处理依赖关系 libfastjson.so.3()(64bit)，它被软件包 libfastjson-devel-0.99.2-1.el6.x86_64 需要
Package libfastjson-0.99.2-1.el6.x86_64 is obsoleted by libfastjson4-0.99.8-1.el6.x86_64 which is already installed
--> 完成依赖关系计算
错误：Package: libfastjson-devel-0.99.2-1.el6.x86_64 (rsyslog_v8)
          Requires: libfastjson = 0.99.2-1.el6
          Available: libfastjson-0.99.1-1.el6.x86_64 (rsyslog_v8)
              libfastjson = 0.99.1-1.el6
          Available: libfastjson-0.99.2-1.el6.x86_64 (rsyslog_v8)
              libfastjson = 0.99.2-1.el6
错误：Package: libfastjson-devel-0.99.2-1.el6.x86_64 (rsyslog_v8)
          Requires: libfastjson.so.3()(64bit)
          Available: libfastjson-0.99.1-1.el6.x86_64 (rsyslog_v8)
              libfastjson.so.3()(64bit)
          Available: libfastjson-0.99.2-1.el6.x86_64 (rsyslog_v8)
              libfastjson.so.3()(64bit)
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest