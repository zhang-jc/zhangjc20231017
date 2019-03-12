title: Jenkins 安装插件网络错误问题解决
date: 2019-03-01 14:24:20
tags:
- Jenkins
categories:
- 开发工具
- Jenkins
---

Jenkins 安装插件时报网络不通的错误，根据错误信息看是连接 https://www.google.com 网络不通。解决这个问题的方法是修改“Update Site”的URL地址，将默认地址由 https 改为 http。
“Update Site”配置项位置在 “插件管理->Advanced”中，修改后如下图：
![Jenkins Update Site](/uploads/20190301/jenkinsUpdateSite.png)
