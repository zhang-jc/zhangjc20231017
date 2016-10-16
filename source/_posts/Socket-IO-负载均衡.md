title: Socket.IO 负载均衡
tags:
  - Socket.IO
  - Nginx
categories:
  - 工具箱
  - Socket.IO
date: 2016-10-16 18:35:34
---


### 架构

![Socket.IO 负载均衡架构](/uploads/20161016/socketio.png)

### Nginx 配置

为了负载均衡时连接保证始终连到一个节点上，使用 Nginx 的 ip_hash 实现 session sticky，让客户端始终连接到集群内一台节点上。

在 Nginx 的 conf.d 目录下创建配置文件 socket_io.conf，内容如下：

    upstream nodejs_websocket {
      ip_hash;
      server 192.168.1.100:3000;
      server 192.168.1.101:3000;
    }

    server {
      listen 80;
      server_name 127.0.0.1 localhost;
      access_log off;
      add_header Content-Type "text/html; charset=UTF-8";

      location / {
        proxy_pass http://nodejs_websocket;

        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
      }
    }

由于 Nginx 的反向代理机制和 Socket.IO 的自动重连机制，上述架构还具备高可用的特性，即当某个节点宕机时，原先连接到该节点上的客户端会自动重连至其它节点上。