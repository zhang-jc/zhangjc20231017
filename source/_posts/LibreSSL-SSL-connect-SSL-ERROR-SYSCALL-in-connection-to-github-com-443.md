---
title: 'LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443'
date: 2021-12-23 15:11:46
tags:
- GitHub
---

向GitHub Push代码的时候出现以下错误：

```Log
$ git push origin master
fatal: unable to access 'https://github.com/zhang-jc/zhangjc.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
```

解决方法：将项目clone协议改为SSH。可以直接修改项目目录下.git/config配置文件中的项目地址。
