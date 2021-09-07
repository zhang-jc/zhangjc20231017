---
title: JConsole远程连接错误解决
date: 2021-09-07 11:53:57
tags:
- Java
- JMX
- JConsole
categories:
- 语言
- Java
---

程序启动命令及参数如下：

```Shellun.management.jmxremote.port=30000 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.managem
$ java -Dcom.sent.jmxremote.ssl=false -jar math-game.jar
```

防火墙已经放开30000端口访问，如下：

```Shell
$ telnet 192.168.72.156 30000
Trying 192.168.72.156...
Connected to 192.168.72.156.
Escape character is '^]'.
```

异常界面如下：

![安全连接失败](/images/20210907/jconsole_security_failed.png)

点击“不安全的连接”异常界面如下：

![非安全连接失败](/images/20210907/jconsole_not_security_failed.png)

最终还是防火墙的问题。网上大都是直接关闭了防火墙，所以问题可以解决。但对于服务器来说，直接关闭防火墙是存在比较大安全隐患的，尤其是暴露在公网上的服务器。用netstat查看进程监听的所有端口，如下：

```Shell
# netstat -ntlp | grep 30000
tcp6       0      0 :::30000                :::*                    LISTEN      24488/java

# netstat -ntlp | grep 24488
tcp6       0      0 :::24605                :::*                    LISTEN      24488/java          
tcp6       0      0 :::19170                :::*                    LISTEN      24488/java          
tcp6       0      0 :::30000                :::*                    LISTEN      24488/java
```

通过上面的信息可以看出，我们启动的应用程序共监听了3个端口。放开3个端口的访问，再次连接成功。