title: CentOS curl 没有到主机的路由
date: 2018-11-23 16:25:09
tags:
- CentOS
- Linux
categories:
- 操作系统
- Linux
---

今天在 yum 安装时遇到网路不通的问题，使用 curl 命令测试如下：

    # curl www.baidu.com
    curl: (7) Failed connect to 172.16.72.69:443; 没有到主机的路由

通过查找发现是服务器设置了代理导致的，代理配置在 /etc/profile 中，如下：

    http_proxy=http://172.16.72.69:80
    http_proxy=http://172.16.72.69:443
    ftp_proxy=http://172.16.72.69:80
    export http_proxy
    export ftp_prox    

该配置也可能会在其他环境变量配置文件中，可自行检查。
