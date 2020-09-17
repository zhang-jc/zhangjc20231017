title: npm 代理
date: 2020-09-17 11:25:50
tags:
- Node.js
categories:
- 开发
- Node.js
---

加快 npm 安装的代理设置：

使用以下命令查看当前的库：

    npm config get registry
    
默认为：https://registry.npmjs.org/。因为是国外的站点，所以会很慢。

用set命令换成阿里的镜像：

    npm config set registry https://registry.npm.taobao.org
