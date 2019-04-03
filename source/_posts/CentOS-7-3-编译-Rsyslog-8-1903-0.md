title: CentOS 7.3 编译 Rsyslog 8.1903.0
date: 2019-04-03 12:18:08
tags:
- CentOS
- Rsyslog
categories:
- 大数据
- Rsyslog
---

源码下载：

    # wget https://github.com/rsyslog/rsyslog/archive/v8.1903.0.tar.gz
    # tar xzvf v8.1903.0.tar.gz

<!-- more -->

创建构建环境：

    # autoreconf -fvi

创建过程中出现以下错误：

    configure.ac:46: error: possibly undefined macro: AC_DISABLE_STATIC
          If this token and others are legitimate, please use m4_pattern_allow.
          See the Autoconf documentation.
    configure.ac:49: error: possibly undefined macro: AC_LIBTOOL_DLOPEN
    configure.ac:52: error: possibly undefined macro: AC_PROG_LIBTOOL
    autoreconf: /usr/bin/autoconf failed with exit status: 1

原因是缺少 libtool，执行以下命令安装：

    # yum install libtool

执行配置，可以自定义选项，我是因为要编译 omhttp 模块，所以使用以下命令：

    # ./configure --enable-omhttp

执行过程汇总出现以下错误：

    checking for LIBESTR... no
    configure: error: Package requirements (libestr >= 0.1.9) were not met:
    
    No package 'libestr' found

使用以下命令检查：

    # rpm -q libestr
    libestr-0.1.9-2.el7.x86_64

安装开发包后解决：

    # yum install libestr-devel

再次执行配置出现以下错误：

    checking for LIBFASTJSON... no
    configure: error: Package requirements (libfastjson >= 0.99.8) were not met:
    
    No package 'libfastjson' found

检查系统安装的包：

    # rpm -q libfastjson
    libfastjson-0.99.4-3.el7.x86_64

安装新版本。源码下载：http://download.rsyslog.com/libfastjson/。下载 libfastjson-0.99.8.tar.gz。

    # wget http://download.rsyslog.com/libfastjson/libfastjson-0.99.8.tar.gz
    # tar xzvf libfastjson-0.99.8.tar.gz
    # cd libfastjson-0.99.8/
    # ./configure --prefix=/usr CC="gcc -m64" PKG_CONFIG_PATH="/usr/lib64/pkgconfig" --libdir=/usr/lib64
    # make
    # make install

再次执行配置出现以下错误：

    checking for LIBUUID... no
    configure: error: Package requirements (uuid) were not met:
    
    No package 'uuid' found

安装 libuuid：

    # yum install libuuid libuuid-devel

再次执行配置出现以下错误：

    configure: error: in `/data/rsyslog/rsyslog-8.1903.0':
    configure: error: libgcrypt-config not found in PATH

安装 libgcrypt 模块：

    # yum install libgcrypt libgcrypt-devel

再次执行配置成功。^_^

执行编译：

    # make

编译过程出现以下错误：

    make[2]: 进入目录“/data/rsyslog/rsyslog-8.1903.0/grammar”
      YACC     grammar.c
    ../ylwrap:行178: yacc: 未找到命令
    make[2]: *** [grammar.c] 错误 127
    make[2]: 离开目录“/data/rsyslog/rsyslog-8.1903.0/grammar”
    make[1]: *** [all-recursive] 错误 1
    make[1]: 离开目录“/data/rsyslog/rsyslog-8.1903.0”
    make: *** [all] 错误 2

安装 byacc：

    # yum install byacc

再次编译出现以下错误：

    make  all-am
    make[3]: 进入目录“/data/rsyslog/rsyslog-8.1903.0/grammar”
      CC       libgrammar_la-grammar.lo
      LEX      lexer.c
      CC       libgrammar_la-lexer.lo
    gcc: error: ./lexer.c: No such file or directory
    gcc: fatal error: no input files

这是因为在 make 前需要执行：

    # sh autogen.sh

编译 omhttp 模块：

    # contrib/omhttp
    # make
        CC       omhttp_la-omhttp.lo
        CCLD     omhttp.la

执行完成后再次 make，生成的 omhttp.so 文件在 contrib/omhttp/.libs 目录下。

至此，大功告成！！O(∩_∩)O哈哈~