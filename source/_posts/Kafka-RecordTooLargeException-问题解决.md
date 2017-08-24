title: Kafka RecordTooLargeException 问题解决
tags:
  - 大数据
  - Kafka
category:
  - 大数据
  - Kafka
date: 2017-08-17 18:50:31
---


Producer 向 Kafka 写入数据时遇到异常：org.apache.kafka.common.errors.RecordTooLargeException。该异常是因为单条消息大小超过限制导致的。解决方法是将限制参数调大：

（1）server端：server.properties  
    message.max.bytes 参数默认值为 1000012，调整为适合的值，如 10485760。

（2）producer端：  
    设置 Producer 的参数 max.request.size 的值与 server 端的 message.max.bytes 值一致。
