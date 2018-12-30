title: Socket.IO 聊天应用实例
tags:
  - Socket.IO
  - Node.js
  - WebSocket
categories:
  - 工具箱
  - Socket.IO
date: 2016-06-05 03:17:11
---

本篇翻译自 Socket.IO 官网的入门实例：<http://socket.io/get-started/chat/>

在本篇指导中，我们将创建一个基本的聊天应用。这个应用几乎不要求事先具有 Node.JS 或 Socket.IO 的基础知识，因此对任意知识水平的用户它都是适合的。

<!-- more -->

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

这样 Express 就安装好了。现在，我们创建应用的 index.js 文件：

    var app = require('express')();
    var http = require('http').Server(app);

    app.get('/', function(req, res){
      res.send('<h1>Hello world</h1>');
    });

    http.listen(3000, function(){
      console.log('listening on *:3000');
    });

说明：

1. Express 初始化 app 作为功能处理器，你可以将它传给 HTTP 服务器（如第 2 行看到的）。
2. 定义一个路由处理器 / ，当我们访问网站主页时就会调用这个处理器。
3. HTTP 服务器监听端口 3000。

执行命令 node index.js 将看到下面的信息：![index](/uploads/20160604/index.png)

在浏览器地址栏输入 http://localhost:3000 访问我们的 Web 应用：![index_page](/uploads/20160604/index_page.png)

### HTML 服务

目前为止，我们在 index.js 中调用 res.send，并且传给它一个 HTML 字符串。如果我们把整个应用的 HTML 都放到这里，代码会非常混乱。应该是，我们创建一个 index.html 文件并用它提供服务。

让我们使用 sendFile 方法重构路由处理器：

    app.get('/', function(req, res){
      res.sendFile(__dirname + '/index.html');
    });

创建 index.html 文件，内容如下：

    <!doctype html>
    <html>
      <head>
        <title>Socket.IO chat</title>
        <style>
          * { margin: 0; padding: 0; box-sizing: border-box; }
          body { font: 13px Helvetica, Arial; }
          form { background: #000; padding: 3px; position: fixed; bottom: 0; width: 100%; }
          form input { border: 0; padding: 10px; width: 90%; margin-right: .5%; }
          form button { width: 9%; background: rgb(130, 224, 255); border: none; padding: 10px; }
          #messages { list-style-type: none; margin: 0; padding: 0; }
          #messages li { padding: 5px 10px; }
          #messages li:nth-child(odd) { background: #eee; }
        </style>
      </head>
      <body>
        <ul id="messages"></ul>
        <form action="">
          <input id="m" autocomplete="off" /><button>Send</button>
        </form>
      </body>
    </html>

重启进程（输入 Control+C 并再次运行 node index），并刷新，页面展示如下：![index_page_2](/uploads/20160604/index_page_2.png)

### 整合 Socket.IO

Socket.IO 由两部分组成：

- 整合（或挂载）了 Node.JS HTTP 服务器的服务器：socket.io
- 在浏览器端加载的客户端库：socket.io-client

开发时，socket.io 自动为我们提供这个客户端，像我们将看到的，因此现在我们仅需要安装一个模块：

    npm install --save socket.io

这个命令将安装这个模块并添加依赖到 package.json。现在修改 index.js：

    var app = require('express')();
    var http = require('http').Server(app);
    var io = require('socket.io')(http);

    app.get('/', function(req, res){
      res.sendfile('index.html');
    });

    io.on('connection', function(socket){
      console.log('a user connected');
    });

    http.listen(3000, function(){
      console.log('listening on *:3000');
    });

注意，通过传递 http（HTTP 服务器）对象初始化了一个 socket.io 的新实例。然后为到来的 socket 监听 connection 事件，并且输入日志到控制台。

现在，在 index.html 中 </body> 前添加下面的片段：

    <script src="/socket.io/socket.io.js"></script>
    <script>
      var socket = io();
    </script>

这将加载 socket.io-client，创建全局变量 io，并且连接。

注意，当调用 io() 的时候没有制定任何 URL，因为它默认尝试连接提供页面服务的主机。

现在重新加载服务器和网站将会看到控制台打印“a user connected”。尝试打开多个标签页，将看到多条消息：![console](/uploads/20160604/console.png)

每个 socket 也监听一个特殊的 disconnect 事件：

    io.on('connection', function(socket){
      console.log('a user connected');
      socket.on('disconnect', function(){
        console.log('user disconnected');
      });
    });

如果多次刷新一个标签页将看到下面的动作：![console2.png](/uploads/20160604/console2.png)

### 发送事件

Socket.IO 背后主要的思想是你可以发送和接收想要的任何事件，携带你想要的任何数据。任何可以编码为 JSON 的对象都可以做到，并且也支持二进制数据。

让我们来实现当一个用户输入一条消息时，服务器作为一个 chat message 事件获取它。在 index.html 中的脚本现在看起来如下：

    <script src="/socket.io/socket.io.js"></script>
    <script src="http://code.jquery.com/jquery-1.11.1.js"></script>
    <script>
      var socket = io();
      $('form').submit(function(){
        socket.emit('chat message', $('#m').val());
        $('#m').val('');
        return false;
      });
    </script>

在 index.js 中我们打印出 chat message 事件：

    io.on('connection', function(socket){
      socket.on('chat message', function(msg){
        console.log('message: ' + msg);
      });
    });

结果像下面的视频展示的：
<p><video autoplay loop width="100%"><source src="/uploads/20160604/video.mp4"></video></p>

### 广播

下一个目标是我们从服务器发送事件给其他用户。

为了发送事件给所有人，Socket.IO 给我们提供了 io.emit：

    io.emit('some event', { for: 'everyone' });

如果你想发送一个确定 socket 的消息给所有人，我们有 broadcast 标识：

    io.on('connection', function(socket){
      socket.broadcast.emit('hi');
    });

在这个例子中，为了简单，为了简单我们发送消息给所有人，包括发送者。

    io.on('connection', function(socket){
      socket.on('chat message', function(msg){
        io.emit('chat message', msg);
      });
    });

在客户端，当我们捕获到一个 chat message 事件，我们把它包含到页面中。客户端完整的代码如下：

    <script>
      var socket = io();
      $('form').submit(function(){
        socket.emit('chat message', $('#m').val());
        $('#m').val('');
        return false;
      });
      socket.on('chat message', function(msg){
        $('#messages').append($('<li>').text(msg));
      });
    </script>

我们的聊天应用就完成了，用了大概 20 行代码！看起来像这样：
<p><video autoplay loop width="100%"><source src="/uploads/20160604/video2.mp4"></video></p>
