title: Centos7.3 安装 Python3
tags:
  - Linux
  - CentOS
  - Python3
categories:
  - 开发
  - 环境搭建
date: 2018-11-19 18:26:32
---


采用源代码构建的方式安装。此处安装的是 Python3.7.1 版本。源代码下载地址：<https://www.python.org/downloads/release/python-371/>。

    # wget https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tar.xz
    --2018-11-19 17:13:28--  https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tar.xz
    正在解析主机 www.python.org (www.python.org)... 151.101.40.223, 2a04:4e42:2d::223
    正在连接 www.python.org (www.python.org)|151.101.40.223|:443... 已连接。
    已发出 HTTP 请求，正在等待回应... 200 OK
    长度：16960060 (16M) [application/octet-stream]
    正在保存至: “Python-3.7.1.tar.xz”

    100%[======================================================================================================>] 16,960,060  4.67MB/s 用时 4.6s   

    2018-11-19 17:13:34 (3.51 MB/s) - 已保存 “Python-3.7.1.tar.xz” [16960060/16960060])

    # xz -d Python-3.7.1.tar.xz
    # tar xvf Python-3.7.1.tar
    # ./configure --prefix=/opt/Python-3.7.1
    # make
    Failed to build these modules:
    _ctypes

以上异常信息是因为 Python3.7.1 需要安装包 libffi-devel：

    # yum install libffi-devel -y

安装完成后重新执行 make 成功。

    # make altinstall
    # ln -s /opt/Python-3.7.1/ /opt/python
    # ln -s /opt/python/bin/python3.7 /usr/bin/python3
    # ln -s /opt/python/bin/pip3.7 /usr/bin/pip3

最后升级新安装的 pip3：

    # pip3 install --upgrade pip

安装完成。
