title: 解决 HTTP POST 请求 Nginx 静态内容 405 错误
date: 2019-04-04 16:09:57
tags:
- Nginx
categories:
- 开发工具
- Nginx
---

Nginx 是不支持 POST 请求静态内容的，通过 POST 请求时出现以下错误：

<!--more-->

    # curl -d "a=b" "http://192.16.36.15:11013/upgrade"
    <html>
    <head><title>405 Not Allowed</title></head>
    <body bgcolor="white">
    <center><h1>405 Not Allowed</h1></center>
    <hr><center>nginx/1.14.2</center>
    </body>
    </html>

解决方法是在 nginx 配置中添加以下配置：

    error_page 405 =200 $uri;

完整配置示例如下：

    server {
        listen       80;
        server_name  localhost;
    
        location /upgrade {
            empty_gif;
            access_log /data/nginx/upgrade.log;
        }
    
        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
    
        error_page 405 =200 $uri;
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    
    }