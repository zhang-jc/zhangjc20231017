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

在 Nginx 的 conf.d 目录下创建配置文件 socket_io.conf，内容如下：

    upstream nodejs_websocket {
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