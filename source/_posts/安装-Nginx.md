title: 安装 Nginx
tags:
  - Nginx
categories:
  - 开发工具
  - Nginx
date: 2016-04-09 23:30:54
---


对于 Linux 平台，[Nginx 安装包](http://nginx.org/en/linux_packages.html) 可以从 nginx.org 下载。

<!-- more -->

**Ubuntu:**

版本&nbsp;&nbsp;&nbsp;&nbsp;Codename&nbsp;&nbsp;&nbsp;&nbsp;支持平台  
12.04&nbsp;&nbsp;&nbsp;precise&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;x86_64, i386  
14.04&nbsp;&nbsp;&nbsp;trusty&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;x86_64, i386, aarch64/arm64  
15.10&nbsp;&nbsp;&nbsp;wily&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;x86_64, i386

在 Debian/Ubuntu 系统上，为了生效 nginx 仓库的签名，并且消除 nginx 安装过程中丢失 PGP 密钥的告警，必须向 apt 程序密钥环添加用来签名 nginx 安装包和仓库的密钥。请从 nginx 网站下载[密钥](http://nginx.org/keys/nginx_signing.key)，并且用下面的命令添加到 apt 程序密钥环中：

> sudo apt-key add nginx_signing.key

用上表中 Ubuntu 发行版本的 codename 替换 *codename* ，然后添加下面内容到 /etc/apt/sources.list 文件的末尾：

> deb http://nginx.org/packages/ubuntu/ *codename* nginx  
> deb-src http://nginx.org/packages/ubuntu/ *codename* nginx

然后执行一下命令完成安装：

> apt-get update  
> apt-get install nginx
