title: Socket.IO Java 客户端
tags:
  - Socket.IO
  - Java
categories:
  - 工具箱
  - Socket.IO
date: 2016-06-14 22:49:33
---

Socket.IO 是一个非常棒的项目；Java 是目前应用非常广的开发语言。两者的结合也是必然的。本篇翻译自 Socket.IO-client Java 项目的 github 主页。

<!-- more -->

[Socket.IO-client Java](https://github.com/socketio/socket.io-client-java) 是 Socket.IO v1.x 的 Java 客户端类库，这个类库是从 [JavaScript client](https://github.com/socketio/socket.io-client) 移植过来的。

参见：

- [Android chat demo](https://github.com/nkzawa/socket.io-android-chat)
- [engine.io-client-java](https://github.com/socketio/engine.io-client-java)

### 安装

最新的包可以从 Maven 的中心仓库获取。你将需要安装[依赖](http://socketio.github.io/socket.io-client-java/dependencies.html)。

> 注意：v0.6.1 之后，包名改为了 io.socket 。请确认更新了依赖配置。

#### Maven

在 pom.xml 中添加依赖：

    <dependencies>
      <dependency>
        <groupId>io.socket</groupId>
        <artifactId>socket.io-client</artifactId>
        <version>0.7.0</version>
      </dependency>
    </dependencies>

#### Gradle

在 build.gradle 中，给 Android Studio 添加 gradle 依赖：

    compile ('io.socket:socket.io-client:0.7.0') {
      // excluding org.json which is provided by Android
      exclude group: 'org.json', module: 'json'
    }

### 使用
Socket.IO-client Java 几乎拥有跟原生 JS 客户端相同的 api 和特性。使用 *IO.socket* 初始化 *Socket* ：

    socket = IO.socket("http://localhost");
    socket.on(Socket.EVENT_CONNECT, new Emitter.Listener() {

      @Override
      public void call(Object... args) {
        socket.emit("foo", "hi");
        socket.disconnect();
      }

    }).on("event", new Emitter.Listener() {

      @Override
      public void call(Object... args) {}

    }).on(Socket.EVENT_DISCONNECT, new  Emitter.Listener() {

      @Override
      public void call(Object... args) {}

    });
    socket.connect();

这个类库使用 [org.json](http://www.json.org/) 解析和构造 JSON 字符串：

    // Sending an object
    JSONObject obj = new JSONObject();
    obj.put("hello", "server");
    obj.put("binary", new byte[42]);
    socket.emit("foo", obj);

    // Receiving an object
    socket.on("foo", new Emitter.Listener() {
      @Override
      public void call(Object... args) {
        JSONObject obj = (JSONObject)args[0];
      }
    });

像下面这样设置选项：

    IO.Options opts = new IO.Options();
    opts.forceNew = true;
    opts.reconnection = false;

    socket = IO.socket("http://localhost", opts);

可以用 *query* 选项设置查询参数。请注意：如果当查询参数改变时不想复用一个缓存的 socket 实例，应该使用 *forceNew* 选项，可能的使用场景是如果你的应用允许用户退出，并且一个新的用户再次登录：

    IO.Options opts = new IO.Options();
    opts.forceNew = true;
    opts.query = "auth_token=" + authToken;
    Socket socket = IO.socket("http://localhost", opts);

当服务器接收到一个消息时，可以用 *Ack* 获取一个回调：

    socket.emit("foo", "woot", new Ack() {
      @Override
      public void call(Object... args) {}
    });

反之亦然：

    // ack from client to server
    socket.on("foo", new Emitter.Listener() {
      @Override
      public void call(Object... args) {
        Ack ack = (Ack) args[args.length - 1];
        ack.call();
      }
    });

SSL (HTTPS, WSS) 设置：

    // default settings for all sockets
    IO.setDefaultSSLContext(mySSLContext);
    IO.setDefaultHostnameVerifier(myHostnameVerifier);

    // set as an option
    opts = new IO.Options();
    opts.sslContext = mySSLContext;
    opts.hostnameVerifier = myHostnameVerifier;
    socket = IO.socket("https://localhost", opts);

查阅 Javadoc 获取更多详情：<http://socketio.github.io/socket.io-client-java/apidocs/>

### Transports 和 HTTP 头

可以像下面这样访问 transports 和它们的 HTTP 头：

    // Called upon transport creation.
    socket.io().on(Manager.EVENT_TRANSPORT, new Emitter.listener() {
      @Override
      public void call(Object... args) {
        Transport transport = (Transport)args[0];

        transport.on(Transport.EVENT_REQUEST_HEADERS, new Emitter.Listener() {
          @Override
          public void call(Object... args) {
            @SuppressWarnings("unchecked")
            Map<String, List<String>> headers = (Map<String, List<String>>)args[0];
            // modify request headers
            headers.put("Cookie", Arrays.asList("foo=1;"));
          }
        });

        transport.on(Transport.EVENT_RESPONSE_HEADERS, new Emitter.Listener() {
          @Override
          public void call(Object... args) {
            @SuppressWarnings("unchecked")
            Map<String, List<String>> headers = (Map<String, List<String>>)args[0];
            // access response headers
            String cookie = headers.get("Set-Cookie").get(0);
          }
        });
      }
    });

### 特性

这个类库支持 JS 客户端支持的所有特性，包括 events、options、upgrading transport。完全支持 Android。

### License

MIT
