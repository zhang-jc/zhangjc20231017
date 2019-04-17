title: CentOS 6.8 安装 Python3 Could not build the ssl module
date: 2018-11-27 19:27:13
tags:
- Linux
- CentOS
- Python3
categories:
- 语言
- Python3
---

在 CentOS 6.8 下编译安装 Python3 时出现以下提示信息：

    Could not build the ssl module!
    Python requires an OpenSSL 1.0.2 or 1.1 compatible libssl with X509_VERIFY_PARAM_set1_host().
    LibreSSL 2.6.4 and earlier do not provide the necessary APIs,     https://github.com/libressl-portable/portable/issues/38

*注意，其实这个只是提示信息，按照下面的步骤可以成功支持 SSL*

###### 第一步：安装 OpenSSL

从 OpenSSL 的官方网站 <https://www.openssl.org/> 下载源代码。此处使用“openssl-1.1.1a.tar.gz”。

依次执行以下命令进行安装：

    #tar xzvf openssl-1.1.1a.tar.gz
    #cd openssl-1.1.1a
    #./config --prefix=/usr/local/openssl shared
    #make
    #make install

*注意，在执行 ./config 的时候一定要指定 --prefix=/usr/local/openssl 参数，方便后面使用。*

验证 OpenSSL 安装时报以下异常信息：

    #openssl version
    openssl: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory

这个是因为 OpenSSL 库的位置没有在系统共享 Lib 目录中导致的。通过创建软连接方式解决。

    #ln -s /usr/local/openssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
    #ln -s /usr/local/openssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
    #openssl version
    OpenSSL 1.1.1a  20 Nov 2018

最后一行表示安装成功。

###### 第二步：安装 LibreSSL

从 LibreSSL 官网 <http://www.libressl.org/> 下载源代码。此处使用“libressl-2.8.2.tar.gz”。

以此执行以下命令安装：

    #wget https://ftp.openbsd.org/pub/OpenBSD/LibreSSL/libressl-2.8.2.tar.gz
    #tar xzvf libressl-2.8.2.tar.gz
    #cd libressl-2.8.2
    #./config
    #make
    #make install

此时再验证 OpenSSL 版本，如下：

    #openssl version
    LibreSSL 2.8.2

###### 第三步：修改 Python 安装配置文件

在 Python2.7.1 源代码目录下修改配置文件  Modules/Setup，将以下前面的注释去掉。

    #SSL=/usr/local/ssl
    #_ssl _ssl.c \
    #        -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
    #        -L$(SSL)/lib -lssl -lcrypto

###### 第四步：安装 Python

开始安装前需要先设置以下环境变量：

export LDFLAGS="-L/usr/local/openssl/lib"
export CPPFLAGS="-I/usr/local/openssl/include"
export PKG_CONFIG_PATH="/usr/local/openssl/lib/pkgconfig"

参考我的另外一篇文章[Centos7.3 安装 Python3](http://zhang-jc.github.io/2018/11/19/Centos7-3-%E5%AE%89%E8%A3%85-Python3/)。

###### 第五步：测试 Python3 https 请求

此处需要取消证书验证。

    #python3
    Python 3.7.1 (default, Nov 28 2018, 17:42:41)
    [GCC 4.4.7 20120313 (Red Hat 4.4.7-17)] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import ssl
    >>> import urllib.request
    >>> context = ssl._create_unverified_context()
    >>> urllib.request.urlopen('https://www.sogou.com/',context=context).read()

测试成功！！
