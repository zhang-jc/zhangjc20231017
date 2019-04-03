title: CentOS 6.5 编译 Rsyslog 8.1903.0
date: 2019-04-03 16:43:16
tags:
---

源码下载参考我的另外一片博文：[CentOS 7.3 编译 Rsyslog 8.1903.0](http://zhang-jc.github.io/2019/04/03/CentOS-7-3-%E7%BC%96%E8%AF%91-Rsyslog-8-1903-0/)。

本篇博文从创建构建环境开始填坑/(ㄒoㄒ)/~~。通过上一篇博文可以解决的问题此处不再赘述。

执行配置时出现以下错误：

    # ./configure --enable-omhttp
    configure: error: cannot find install-sh or install.sh in "." "./.." "./../.."

先执行 autogen.sh 出现以下错误：

    # sh autogen.sh
    checking for LIBESTR... configure: error: Package requirements (libestr >= 0.1.9) were not met:
    No package 'libestr' found

检查没有安装 libestr 库，所以重新安装：

    # yum install libestr libestr-devel

再次执行 autogen.sh 问题依旧o(╯□╰)o。