title: Redis 初探
tags:
  - Redis
categories:
  - 工具箱
  - Redis
date: 2016-05-31 20:57:20
---

### 安装
Redis 下载地址及安装说明官方地址：<http://redis.io/download>。Redis 目前没有官方支持的 Windows 平台版本。

用以下命令下载、解压、编译 Redis：

    $ wget http://download.redis.io/releases/redis-3.2.0.tar.gz
    $ tar xzf redis-3.2.0.tar.gz
    $ cd redis-3.2.0
    $ make

编译之后的二进制文件在 src 目录下。用以下命令启动 Redis：

    $ src/redis-server

可以用 Redis 内置的客户端与 Redis 交互：

    $ src/redis-cli
    redis> set foo bar
    OK
    redis> get foo
    "bar"

### Java 客户端

Redis 分语言的客户端列表可以从官网链接查看：<http://redis.io/clients>。Jedis 是推荐的 Java 客户端，并且近 6 个月有更新，所以选用该客户端。Jedis 的 github 项目链接：<https://github.com/xetorthio/jedis>

Mave 依赖配置：

    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.8.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>

使用：

    Jedis jedis = new Jedis("localhost");
    jedis.set("foo", "bar");
    String value = jedis.get("foo");
