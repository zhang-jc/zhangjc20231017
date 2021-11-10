---
title: npm安装时无法访问github域名的解决方法
date: 2021-11-10 17:29:32
tags:
- GitHub
- NPM
---

今天在用npm install的时候出现了github项目访问不了的异常：

```Log
npm ERR! Error while executing:
npm ERR! /bin/git ls-remote -h -t https://github.com/nhn/raphael.git
npm ERR! 
npm ERR! fatal: unable to access 'https://github.com/nhn/raphael.git/': Encountered end of file
npm ERR! 
npm ERR! exited with error code: 128

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/ycs/.npm/_logs/2021-11-10T09_14_23_017Z-debug.log
```

ping及curl测试都无法访问。但服务器是可以访问互联网的，怀疑是被墙了。

从网址[https://websites.ipaddress.com/github.com](https://websites.ipaddress.com/github.com)查询GitHub的IP地址。

![GitHub IP](/images/20211110/github-ip.png)

添加hosts配置：

```Text
140.82.114.3 github.com
```

问题解决。