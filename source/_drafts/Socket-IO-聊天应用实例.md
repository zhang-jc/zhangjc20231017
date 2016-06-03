title: Socket.IO 聊天应用实例
tags:
- Socket.IO
- Node.JS
- WebSocket
categories:
- 工具箱
- Socket.IO
---
本篇翻译自 Socket.IO 官网的入门实例：<http://socket.io/get-started/chat/>

在本篇指导中，我们将创建一个基本的聊天应用。这个应用几乎不要求事先具有 Node.JS 或 Socket.IO 的基础知识，因此对任意知识水平的用户它都是适合的。

### 引言

使用流行的 Web 应用工具栈（如 LAMP（PHP））写一个聊天应用通常是非常困难的。这涉及到从服务器拉取变化的信息，保持时间戳的跟踪，并且这比应该需要的速度慢很多。

通常是大多数实时聊天系统都是围绕 Sockets 设计解决方案，在客户端和服务端提供一个双向通信通道。

这意味着服务端可以推送消息给客户端。其思想是，每当你写了一个聊天消息，服务端将获取它，并把它推送给所有其他连接的客户端。

### Web 框架

第一个目标是，设置一个提供提供表单和消息列表的简单 HTML 网页。我们将使用 Node.JS Web 框架 express 来达到目的。确保已经[安装了 Node.JS](https://nodejs.org/en/)。

首先，创建清单文件 package.json 来描述我们的项目。建议你将这个文件放在专门的一个空目录下（如，chat-example）。

    {
      "name": "socket-chat-example",
      "version": "0.0.1",
      "description": "my first socket.io app",
      "dependencies": {}
    }

现在，为了方便迁移需要的依赖，我们使用 npm install --save:

    npm install --save express@4.10.2
