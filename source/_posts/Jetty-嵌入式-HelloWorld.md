title: Jetty 嵌入式 HelloWorld
tags:
  - Jetty
categories:
  - 开发工具
  - Jetty
date: 2016-04-10 18:08:48
---


### 简介

Jetty 是一个开源的项目，提供 HTTP 服务端、HTTP 客户端和 javax.servlet 容器。  
下面看如何利用 Jetty 的 API 快速开发嵌入式代码。

<!-- more -->

### 下载 Jar 包

Jetty 被分解很多 jar 包和依赖包，通过选择 jar 包的最小集合达到占用最小空间的目的。通常，最好使用像 Maven 一样的工具管理 jar 包，但本例中使用包含 Jetty 所有类的聚合 jar 包。可以用 [curl](https://curl.haxx.se/) 命令或者浏览器手动下载 [jetty-all.jar](http://central.maven.org/maven2/org/eclipse/jetty/aggregate/jetty-all/9.3.7.v20160115/jetty-all-9.3.7.v20160115-uber.jar)。

像下面这样使用 curl 命令：

	mkdir Demo
	cd Demo
	curl -o jetty-all-uber.jar http://central.maven.org/maven2/org/eclipse/jetty/aggregate/jetty-all/9.3.7.v20160115/jetty-all-9.3.7.v20160115-uber.jar

### HelloWorld

Jetty 官方文档中的 [嵌入 Jetty](http://www.eclipse.org/jetty/documentation/current/advanced-embedding.html)一章有很多通过 Jetty API 编写的实例。本教程用只包含一个 Main 方法的简单 HelloWorld 处理器运行服务器。[HelloWorld.java](https://raw.githubusercontent.com/eclipse/jetty.project/master/examples/embedded/src/main/java/org/eclipse/jetty/embedded/HelloWorld.java) 文件代码如下：

	package org.eclipse.jetty.embedded;

	import java.io.IOException;
	import javax.servlet.ServletException;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	import org.eclipse.jetty.server.Request;
	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.handler.AbstractHandler;

	public class HelloWorld extends AbstractHandler {

	    @Override
	    public void handle(String target, Request baseRequest, HttpServletRequest request, HttpServletResponse response) throws IOException, ServletException {
	        response.setContentType("text/html; charset=utf-8");
	        response.setStatus(HttpServletResponse.SC_OK);
	        response.getWriter().println("<h1>Hello World</h1>");
	        baseRequest.setHandled(true);
	    }

	    public static void main(String[] args) throws Exception {
	        Server server = new Server(8080);
	        server.setHandler(new HelloWorld());
	        server.start();
	        server.join();
	    }
	}

### 编译 HelloWorld

下面的命令编译 HelloWorld 类：

	mkdir classes
	javac -d classes -cp jetty-all-uber.jar HelloWorld.java

### 运行处理器和服务器

下面的命令运行 HelloWorld 实例：

	java -cp classes:jetty-all-uber.jar org.eclipse.jetty.embedded.HelloWorld

在浏览器地址栏输入：[http://localhost:8080](http://localhost:8080) 查看 HelloWorld 页面。
