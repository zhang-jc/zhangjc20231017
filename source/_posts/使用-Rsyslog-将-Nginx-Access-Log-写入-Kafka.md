title: 使用 Rsyslog 将 Nginx Access Log 写入 Kafka
date: 2019-03-15 09:41:45
tags:
- Rsyslog
- Nginx
- Kafka
categories:
- 大数据
- Rsyslog
---
### 环境说明

- CentOS Linux release 7.3.1611
- kafka_2.12-0.10.2.2
- nginx/1.12.2
- rsyslog-8.24.0-34.el7.x86_64.rpm

<!-- more -->

### 创建测试 Topic

    $ ./kafka-topics.sh --zookeeper 192.168.72.25:2181/kafka --create --topic develop-test-topic --partitions 10 --replication-factor 3

### Rsyslog 安装

一般系统自带 Rsyslog 服务无需另外安装。但是因为数据需要通过 Rsyslog 的 omkafka 模块写入到 Kafka，而 omkafka 在 Rsyslog 的 v8.7.0+ 版本才支持，所以要看当前系统中 Rsyslog 的版本是否需要升级。使用以下命令查看：

    # rsyslogd -v
    rsyslogd 7.4.7, compiled with:
    	FEATURE_REGEXP:				Yes
    	FEATURE_LARGEFILE:			No
    	GSSAPI Kerberos 5 support:		Yes
    	FEATURE_DEBUG (debug build, slow code):	No
    	32bit Atomic operations supported:	Yes
    	64bit Atomic operations supported:	Yes
    	Runtime Instrumentation (slow code):	No
    	uuid support:				Yes

    See http://www.rsyslog.com for more information.

执行以下命令安装：

    # sudo yum install rsyslog

安装依赖关系如下：![](/uploads/20190315/Rsyslog依赖关系.png)

### 添加 omkafka 模块

    # yum install rsyslog-kafka

### Rsyslog Client Nginx 配置

注意，Nginx 1.7.1 之后才支持 syslog 的方式处理日志。具体配置项参见官网文档[Logging to syslog](https://nginx.org/en/docs/syslog.html)。

Nginx 配置主要是日志格式和 Access Log 配置项：

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    server {
        listen 11000;

        location / {
            proxy_pass http://10.16.0.144:11000;
            access_log syslog:server=localhost,facility=local7,tag=nginx11000Root,severity=info main;
        }
    }

### Rsyslog Server 端配置

Rsyslog 的主配置文件 /etc/rsyslog.conf，其中会包含引入 /etc/rsyslog.d 下扩展名为 conf 的配置文件。在 /etc/rsyslog.d 目录下创建 rsyslog_nginx_kafka_cluster.conf，配置内容如下：

    module(load="imudp")
    input(type="imudp" port="514")

    # nginx access log ==> rsyslog server(local) ==> kafka
    module(load="omkafka")

    template(name="nginx-11000-root" type="string" string="%msg%")

    if $inputname == "imudp" then {
        if ($programname == "nginx11000Root") then
            action(type="omkafka"
                template="nginx-11000-root"
                broker=["192.168.72.10:9092","192.168.72.20:9092","192.168.72.25:9092","192.168.72.26:9092","192.168.72.27:9092","192.168.72.48:9092","192.168.72.55:9092","192.168.72.80:9092","192.168.72.81:9092","192.168.72.97:9092"]
                topic="develop-test-topic"
                partitions.auto="on"
                confParam=[
                    "socket.keepalive.enable=true"
                ]
            )
    }

    :rawmsg, contains, "nginx11000Root" ~

### 联调测试

启动 Rsyslog 服务：

    # service rsyslog start
    Redirecting to /bin/systemctl start  rsyslog.service

### 遇到的问题
#### syslog tag 只能包含字母和数字

    # nginx -t
    nginx: [emerg] syslog "tag" only allows alphanumeric characters and underscore in     /etc/nginx/conf.d/jx-11000-jenkins149-36-144.conf:7
    nginx: configuration file /etc/nginx/nginx.conf test failed

#### 'omkafka' is unknown

Rsyslog 中没有包含 omkafka 模块，需要另外安装。查看 /var/log/messages 日志信息会有以下提示：

    # tail -f messages
    Mar 15 15:13:40 192-168-72-29 systemd: Started System Logging Service.
    Mar 15 15:13:40 192-168-72-29 rsyslogd: could not load module '/usr/lib64/rsyslog/omkafka.so', dlopen:     /usr/lib64/rsyslog/omkafka.so: cannot open shared object file: No such file or directory  [v8.24.0-34.el7 try     http://www.rsyslog.com/e/2066 ]
    Mar 15 15:13:40 192-168-72-29 rsyslogd: could not load module '/usr/lib64/rsyslog/omkafka.so', dlopen:     /usr/lib64/rsyslog/omkafka.so: cannot open shared object file: No such file or directory  [v8.24.0-34.el7 try     http://www.rsyslog.com/e/2066 ]
    Mar 15 15:13:40 192-168-72-29 rsyslogd: module name 'omkafka' is unknown [v8.24.0-34.el7 try     http://www.rsyslog.com/e/2209 ]
    Mar 15 15:13:40 192-168-72-29 rsyslogd: module name 'omkafka' is unknown [v8.24.0-34.el7 try     http://www.rsyslog.com/e/2209 ]

#### CentOS 6.5 升级 Rsyslog
