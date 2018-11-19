title: Python3 操作 MongoDB 批量 upsert
tags:
  - Python3
  - MongoDB
categories:
  - 语言
  - Python3
date: 2018-11-19 16:18:25
---


代码如下：

    mongoClient = MongoClient('mongodb://172.16.72.213:27017/')
    opsDb = mongoClient.ops
    azScheduled = opsDb.azScheduledFlow
    
    bulkOpers = []
    for flow in scheduledFlows.values():
        bulkOpers.append(UpdateOne({'opsDt': opsDt, 'projectId': flow['projectId'], 'projectName': flow['projectName'], 'flowName': flow['flowName']}, {'$set': {'opsDateTime': opsDtStr, 'status': flow['status'], 'startTime': flow['startTime'], 'endTime': flow['endTime'], 'elapsed': flow['elapsed']}}, upsert=True))
    
    azScheduled.bulk_write(bulkOpers)
