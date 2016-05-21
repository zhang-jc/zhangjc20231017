title: 基于 hexo + github 的个人博客系统搭建
tags:
  - hexo
  - github
  - blog
  - Oray
  - 个人博客
categories:
  - 开发
  - nodejs
date: 2016-05-21 22:58:56
---

### 工具介绍
#### Hexo

Hexo 是一个快速、简单，并且强大的博客框架。主要有以下特性：

1. Node.js 带来不可思议的生成速度。建立数百个文件只需要几秒钟。

2. 支持 GitHub 所有推荐的 Markdown 特性。你甚至可以在 Hexo 中使用大部分 Octopress 的插件。

3. 只需要一个命令就可以部署个人博客到 GitHub、Heroku 或者其他站点。

4. Hexo 有一个强大的插件系统。可以安装很多 Jade 插件和 CoffeeScript 插件。

#### github

GitHub 提供一个快速的、灵活的、协作的开发过程，让你可以自己工作或者跟其他人一起工作。

此处主要利用 GitHub 提供的 GitHub pages 特性。详细说明请查看[官方文档](https://pages.github.com/)。

#### Oray

Oray 是一家域名服务提供商。可以根据自己的喜好选择其他域名服务商，如万网。域名服务一般都会提供 URL 跳转的功能：访问域名可跳转到一个网址上，实现不加端口号即可访问服务器（显性URL，会改变地址栏中的网址，隐性URL则反之）。此处正式利用这种功能。

### 整体架构

![个人博客架构](/uploads/20160521/personal-blog-site.png)

1. source repository（local）：本地代码仓库。在本地搭建 node.js 环境，并运行个人博客系统作为开发调试环境。
2. source repository（github）：github 上创建的代码仓库。通过 git push 将个人代码提交到 github 代码仓库。
3. static repository（local）：个人博客在本地生成的静态内容仓库。使用 hexo generate 命令完成。
4. 使用 Oray 域名服务的原因是可以使用自己喜欢的个性化的域名。

### 总结

采用这种方式作为个人博客的好处是：
1. 过程简单，只要有基础英文文档阅读能力。按照官网介绍操作就好。不用开发也可以完成。
2. 基于 GitHub 服务稳定；
3. Hexo 功能强大，插件丰富，而且有丰富的主题可以选择。
4. 文章编写使用 Markdown，能让你更专注于文章内容，不用关心展示。
5. 成本极低。只需要域名服务付费，每年不到一百元。
