title: Socket.IO Java 客户端
tags:
- Socket.IO
- Java
categories:
- 工具箱
- Socket.IO
---
Socket.IO 是一个非常棒的项目；Java 是目前应用非常广的开发语言。两者的结合也是必然的。本篇翻译自 Socket.IO-client Java 项目的 github 主页，并结合实例说明。

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
