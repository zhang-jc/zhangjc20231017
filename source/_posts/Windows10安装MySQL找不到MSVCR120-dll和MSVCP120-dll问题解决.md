---
title: Windows10安装MySQL找不到MSVCR120.dll和MSVCP120.dll问题解决
date: 2022-01-18 18:02:18
tags:
- Windows
- MySQL
- 操作系统
categories:
- 数据库
- MySQL
---

msvcp120.dll、msvcr120.dll、vcomp120.dll属于VC++2013版中的动态链接库，如果丢失重新安装VC++2013即可。下载地址：[https://www.microsoft.com/zh-cn/download/confirmation.aspx?id=40784](https://www.microsoft.com/zh-cn/download/confirmation.aspx?id=40784)。