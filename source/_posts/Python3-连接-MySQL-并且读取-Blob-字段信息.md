title: Python3 连接 MySQL 并且读取 Blob 字段信息
tags:
  - Python3
  - MySQL
  - Azkaban
categories:
  - 语言
  - Python3
date: 2018-11-15 14:33:28
---


#### 安装驱动

    $ pip3 install mysql-connector-python
    Command 'pip3' not found, but can be installed with:
    sudo apt install python3-pip
    
根据提示信息安装 pip3。

根据 MySQL 官网建议应该安装 8.0 的驱动。我的安装：mysql-connector-python-8.0.13、protobuf-3.6.1、setuptools-40.6.2、six-1.11.0。

#### 读取MySQL数据

以读取 Azkaban 中的 triggers 表数据为例。代码如下：

    #!/usr/bin/python3
    
    import mysql.connector
    import gzip
    
    config = {
        'user': 'roHive',
        'password': 'hive@bigdata!23',
        'host': '172.16.72.22',
        'database': 'azkaban3',
        'raise_on_warnings': True,
        'charset': 'latin1'
    }
    
    cnx = mysql.connector.connect(**config)
    cursor = cnx.cursor()
    query = ("SELECT trigger_id, data FROM azkaban3.triggers")
    cursor.execute(query)
    
    for (triggerId, triggerData) in cursor:
        print(f'triggerId={triggerId}')
    
    cursor.close()
    cnx.close()
    
Azkaban 的 triggers 表中的 data 字段是 BLOB 类型。因为我的 Azkaban MySQL 库采用的是 latin1 编码，如果连接时不设置字符集在读取 data 字段数据时在读取 BLOB 类型字段时会报如下错误：

    UnicodeDecodeError: 'utf-8' codec can't decode byte 0x8b in position 1: invalid start byte

因为 data 字段是采用 gzip 压缩的，所以需要解压，代码如下：

    for (triggerId, triggerData) in cursor:
        print(gzip.decompress(bytes(triggerData, encoding='latin1')))
