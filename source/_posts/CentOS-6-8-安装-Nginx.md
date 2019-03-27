title: CentOS 6.8 安装 Nginx
date: 2019-03-27 15:09:45
tags:
- Nginx
- CentOS
categories:
- 开发工具
- Nginx
---
提前安装：

    # sudo yum install yum-utils

一般情况下这个工具系统已经安装。

<!--more-->

创建文件 /etc/yum.repos.d/nginx.repo，输入内容如下：

    [nginx-stable]
    name=nginx stable repo
    baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
    gpgcheck=1
    enabled=1
    gpgkey=https://nginx.org/keys/nginx_signing.key
    
    [nginx-mainline]
    name=nginx mainline repo
    baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
    gpgcheck=1
    enabled=0
    gpgkey=https://nginx.org/keys/nginx_signing.key

注意，根据自己服务器的情况替换参数 $releasever 和 $basearch。

一般默认使用稳定安装包。如果想使用主线安装包则运行以下命令：

    # sudo yum-config-manager --enable nginx-mainline

运行以下命令安装 Nginx：

    # sudo yum install nginx