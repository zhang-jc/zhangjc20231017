title: CentOS 6.8 Python3 遇到的坑
date: 2018-11-30 15:46:37
tags:
- CentOS
- Python3
- MySQL
- Linux
---

##### python3: error while loading shared libraries: libssl.so.1.1

这个问题是因为安装 OpenSSL 时是用 root 用户安装，对于其他用户没有读和执行的权限。解决方法是将 OpenSSL 安装目录及下面的文件权限改为 755。

CentOS 6.8 下安装 Python3 并启用 SSL 模块请参考另外一篇博文：[CentOS 6.8 安装 Python3 Could not build the ssl module](http://zhang-jc.github.io/2018/11/27/CentOS-6-8-%E5%AE%89%E8%A3%85-Python3-Could-not-build-the-ssl-module/)。

##### ModuleNotFoundError: No module named 'mysql'

使用 pip3 安装完 mysql-connector-python-8.0.13 后，在 root 用户下测试没问题，但是其他用户使用时出现找不到的错误。所以还是权限的问题。将 Python3 整个安装目录及下面的文件和子目录权限都改为 755 问题解决。

##### _mysql_connector.MySQLInterfaceError: Bad handshake

完整异常信息如下：

    Traceback (most recent call last):
      File "/opt/python/lib/python3.7/site-packages/mysql/connector/connection_cext.py", line 176, in _open_connection
        self._cmysql.connect(**cnx_kwargs)
    _mysql_connector.MySQLInterfaceError: Bad handshake

    During handling of the above exception, another exception occurred:

    Traceback (most recent call last):
      File "/data/jenkins/releases/ops/DataWarehouse/azkabanJx.py", line 26, in <module>
        cnx = mysql.connector.connect(**config)
      File "/opt/python/lib/python3.7/site-packages/mysql/connector/__init__.py", line 172, in connect
        return CMySQLConnection(*args, **kwargs)
      File "/opt/python/lib/python3.7/site-packages/mysql/connector/connection_cext.py", line 78, in __init__
        self.connect(**kwargs)
      File "/opt/python/lib/python3.7/site-packages/mysql/connector/abstracts.py", line 731, in connect
        self._open_connection()
      File "/opt/python/lib/python3.7/site-packages/mysql/connector/connection_cext.py", line 179, in _open_connection
        sqlstate=exc.sqlstate)
    mysql.connector.errors.OperationalError: 1043 (08S01): Bad handshake

Google 后了解到原因是：

> The C extension was added in version 2.1.1 and is enabled by default as of 8.0.11. The use_pure option determines whether the Python or C version of this connector is enabled and used.1

服务器上没有 C 扩展，所以需要设置参数 'use_pure': True。完整配置样例如下：

    config = {
      'user': 'scott',
      'password': 'password',
      'host': '127.0.0.1',
      'database': 'employees',
      'use_pure': True,
    }

*注意，添加参数 'use_pure': True 之后，从 MySQL 中读取的 BLOB 字段是字节数组类型。*
