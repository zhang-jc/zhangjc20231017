title: Nginx 配置支持 HTTPS 代理
date: 2019-03-12 15:39:01
tags:
- Nginx
categories:
- 开发工具
- Nginx
---

本文描述的是 Nginx HTTPS 反向代理的情况（即后端服务是 HTTP 的）。

#### 使用 openssl 配置 ssl 证书

##### 生成服务器端的私钥（key 文件）：

    # openssl genrsa -des3 -out server.key 2048
    Generating RSA private key, 2048 bit long modulus
    .............+++
    ..........+++
    e is 65537 (0x10001)
    Enter pass phrase for server.key:

输入两次密码后 key 文件生成完毕。

##### 生成 CSR（Certificate Signing Request）文件：

    # openssl req -new -key server.key -out server.crs

根据提示输入需要的信息后 CSR 文件生成完毕。

##### 生成自签名的 CA 文件：

    # openssl x509 -req -days 3650 -in server.crs -signkey server.key -out ca.crt
    Signature ok
    subject=/C=cn/ST=beijing/L=beijing/O=test/OU=test/CN=test/emailAddress=test@test.com
    Getting Private key
    Enter pass phrase for server.key:

输入 key 文件的密码后 CA 文件生成完毕。

#### Nginx 配置

    server {
        listen 443;
        ssl on;
        ssl_certificate ca.crt;
        ssl_certificate_key server.key;

        location / {
            proxy_pass http://192.168.36.144:11000;
         }
    }

重新加载 Nginx 配置文件后，发现 Nginx 代理的 443 端口未正常启动。查看 Nginx error 日志发现以下异常信息：

    2019/03/12 16:49:26 [emerg] 85175#0: SSL_CTX_use_PrivateKey_file("/etc/nginx/server.key") failed (SSL: error:0906406D:PEM routines:PEM_def_callback:problems getting password error:0906A068:PEM routines:PEM_do_header:bad password read error:140B0009:SSL routines:SSL_CTX_use_PrivateKey_file:PEM lib)

通过以下方式解决：

    # mv server.key server.key.org
    # openssl rsa -in server.key.org -out server.key

重新加载 Nginx 配置文件。此时也不需要再输入 key 文件的密码了。
