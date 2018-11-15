title: Python3连接MongoDB并写入数据
tags:
  - Python3
  - MongoDB
categories:
  - 语言
  - Python3
date: 2018-11-15 17:09:30
---


#### 安装 PyMongo

    $ pip3 install pymongo
    Successfully installed pymongo-3.7.2

#### 连接MongoDB并且批量插入操作

    #!/usr/bin/python3

    import mysql.connector
    import gzip
    import json
    from pymongo import MongoClient
    from datetime import datetime
    
    opsDateTime = datetime.now().isoformat(timespec='seconds')
    
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
    
    scheduledList = []
    for (triggerId, triggerData) in cursor:
        triggerJson = json.loads(gzip.decompress(bytes(triggerData, encoding='latin1')))
        actionJson = triggerJson['actions'][0]['actionJson']
        projectName = actionJson['projectName']
        flowName = actionJson['flowName']
        scheduledFlow = {'opsDateTime': opsDateTime, 'projectName': projectName, 'flowName': flowName}
        scheduledList.append(scheduledFlow)
    
    cursor.close()
    cnx.close()
    
    mongoClient = MongoClient('mongodb://172.16.72.213:27017/')
    opsDb = mongoClient.ops
    azScheduled = opsDb.azScheduledFlow
    azScheduled.insert_many(scheduledList)
    
