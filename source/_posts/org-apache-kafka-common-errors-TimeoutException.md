title: org.apache.kafka.common.errors.TimeoutException
tags:
  - 大数据
  - Kafka
categories:
  - 大数据
  - Kafka
date: 2017-08-24 12:24:19
---


使用 kafka-console-producer.sh 向远端 Kafka 写入数据时遇到以下错误：

    $ bin/kafka-console-producer.sh --broker-list 172.16.72.202:9092 --topic test
    This is a message
    [2017-08-24 11:47:48,286] ERROR Error when sending message to topic test with key: null, value: 17 bytes with error: (org.apache.kafka.clients.producer.internals.ErrorLoggingCallback)
    org.apache.kafka.common.errors.TimeoutException: Expiring 1 record(s) for test-0: 1523 ms has passed since batch creation plus linger time
    八月 24, 2017 11:50:55 上午 sun.rmi.transport.tcp.TCPTransport$AcceptLoop executeAcceptLoop
    警告: RMI TCP Accept-0: accept loop for ServerSocket[addr=0.0.0.0/0.0.0.0,localport=38175] throws
    java.io.IOException: The server sockets created using the LocalRMIServerSocketFactory only accept connections from clients running on the host where the RMI remote objects have been exported.
    	at sun.management.jmxremote.LocalRMIServerSocketFactory$1.accept(LocalRMIServerSocketFactory.java:114)
    	at sun.rmi.transport.tcp.TCPTransport$AcceptLoop.executeAcceptLoop(TCPTransport.java:400)
    	at sun.rmi.transport.tcp.TCPTransport$AcceptLoop.run(TCPTransport.java:372)
     	at java.lang.Thread.run(Thread.java:748)
        
在没有配置 advertised.host.name 的情况下，Kafka 并没有广播我们配置的 host.name，而是广播了主机配置的 hostname。远端的客户端并没有配置 hosts，所以自然是连接不上这个 hostname 的，所以在远端客户端配置 hosts。在客户端 /etc/hosts 中添加以下内容后问题解决：

    172.16.72.202 172-16-72-202
