title: Flume1.9支持Kafka Record的Header信息读取
date: 2021-05-20 11:46:23
tags:
- Flume
- Kafka
categories:
- 大数据
- Flume
---

在新版本Kafka中为了更好的支持业务扩展，消息格式增加了消息级别的Header信息。但是Flume从Kafka中读取数据时只处理了Avro格式中的Header信息。解决这一问题修改Flume源代码如下：

org.apache.flume.source.kafka.KafkaSource.java类中的doProcess方法第280行处添加以下代码：

```Java
        Headers messageHeaders = message.headers();
        for (Header header : messageHeaders) {
          String key = header.key();
          String value = new String(header.value());

          if (!headers.containsKey(key)) {
            headers.put(key, value);
          }
        }
```

[完整的Java文件](/uploads/20210520/KafkaSource.java)