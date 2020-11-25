title: Hue Load Balance配置
date: 2020-11-18 17:11:29
tags:
- Hue
- Nginx
categories:
- 大数据
- Hue
---

直接上配置：

	server {
	  server_name 192.168.72.31;
	  listen 8001;
	  
	  charset utf-8;
	  
	  proxy_connect_timeout 600s;
	  proxy_read_timeout 600s;
	  proxy_send_timeout 600s;
	  
	  location / {
	    proxy_set_header Host $http_x_forwarded_for;
	    proxy_set_header X-Forwarded-For $http_x_forwarded_for;
	    
	    proxy_pass http://hue;
	  }
	}
	
	upstream hue {
	  hash $cookie_sessionid;
	  
	  server 192.168.72.22:8888 max_fails=3;
	  server 192.168.72.31:8888 max_fails=3;
	}

重点是调整 upstream hash 的策略。因为 Hue 是需要保持 session 的，同一个 session 的请求需要发送到同一台后端服务器上。简单的可以采用 ip_hash 策略，这个策略存在两个重要的问题：

- 对隐藏在局域网后的用户不起作用。
- ip_hash 只取 IPV4 的前三段值做 Hash，在非公网大并发场景下会出现负载非常不均衡的情况。

基于以上两点修改了 Nginx 的配置采用 $cookie_sessionid 做为 hash 值，达到 session 级别的负载均衡。

主要的问题其实是这种方式未做到按照后端服务器实际负载再按照 session 级别来分配请求。后续打算研究 HAProxy 是否可以做到。