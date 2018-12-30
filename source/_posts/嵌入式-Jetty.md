title: 嵌入式 Jetty
tags:
  - Jetty
categories:
  - 开发工具
  - Jetty
date: 2016-04-15 15:38:23
---


Jetty 有一个口号，“不要在 Jetty 中部署你的应用，部署 Jetty 在你的应用中！”这个口号意味着构建应用为为标准的 WAR 并且部署到 Jetty 的另外一个选择是，Jetty 被设计为一个软件组件，可以在 Java 程序中像其他 POJO 一样被实例化和使用。换一种方式，以嵌入式模式运行 Jetty 意味着放置一个 HTTP 模块到你的应用中，而不是放置你的应用到 HTTP 服务器。

<!-- more -->

本教程将带领你一步步从最简单的 Jetty 服务器实例化到用标准化部署描述器运行多 Web 应用程序。大部分这些实例的代码是标准 Jetty 项目的一部分。

### 概述

嵌入 Jetty 服务器，下面是通常的步骤，也是本教程实例中展示步骤：

1. 创建一个 [Server](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/Server.html) 实例。  
2. 添加/配置 [Connectors](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/Connector.html)。  
3. 添加/配置 [Handlers](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/Handler.html)、[Contexts](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/handler/ContextHandler.html)、[Servlets](http://docs.oracle.com/javaee/6/api/javax/servlet/Servlet.html)。  
4. 启动 Server。  
5. 服务器等待或者用自己的线程做一些其他的事情。

### 创建 Server

下面是 SimplestServer.java 文件的代码，实例化并且运行一个可能最简单的 Jetty server。

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Server;

	/**
	 * The simplest possible Jetty server.
	 */
	public class SimplestServer
	{
	    public static void main( String[] args ) throws Exception
	    {
	        Server server = new Server(8080);
	        server.start();
	        server.dumpStdErr();
	        server.join();
	    }
	}

这将运行一个监听 8080 端口的 HTTP Server。这不是一个有用的 server，因为它没有处理器，因此对所有的请求都将返回 404。

### 使用处理器

为了针对请求生成响应，Jetty 要求在服务器上设置处理器。一个处理器可能：

- 检查/修改 HTTP 请求。
- 生成完整的 HTTP 响应。
- 调用其他处理器（见 HandlerWrapper）。
- 选择一个或者多个处理器调用（见 HandlerCollection）。

#### HelloWorld Handler

下面 HelloHandler.java 的代码展示了一个简单的 hello world 处理器：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import java.io.IOException;
	import java.io.PrintWriter;

	import javax.servlet.ServletException;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;

	import org.eclipse.jetty.server.Request;
	import org.eclipse.jetty.server.handler.AbstractHandler;

	public class HelloHandler extends AbstractHandler
	{
	    final String greeting;
	    final String body;

	    public HelloHandler()
	    {
	        this("Hello World");
	    }

	    public HelloHandler( String greeting )
	    {
	        this(greeting, null);
	    }

	    public HelloHandler( String greeting, String body )
	    {
	        this.greeting = greeting;
	        this.body = body;
	    }

	    public void handle( String target,
	                        Request baseRequest,
	                        HttpServletRequest request,
	                        HttpServletResponse response ) throws IOException,
	                                                      ServletException
	    {
	        response.setContentType("text/html; charset=utf-8");
	        response.setStatus(HttpServletResponse.SC_OK);

	        PrintWriter out = response.getWriter();

	        out.println("<h1>" + greeting + "</h1>");
	        if (body != null)
	        {
	            out.println(body);
	        }

	        baseRequest.setHandled(true);
	    }
	}

传给处理方法的参数是：

- target - 请求的目标，可能是一个 URI 或者被命名调度器的名称。
- baseRequest - 可变的 Jetty 请求对象，这个对象总是未包装的。
- request - 不变的请求对象，可能被一个过滤器或 servlet 包装过。
- response - 响应，可能被过滤器或 servlet 包装过。

处理器设置响应的状态、内容类型，并且在使用写入器生成响应体之前标记请求已经被处理。

#### 运行 HelloWorldHandler

为了允许一个处理器处理 HTTP 请求，必须添加处理器到一个服务器实例中。下面 OneHandler.java 代码展示了 Jetty 服务器如何使用 HelloWrold 处理器：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Server;

	public class OneHandler
	{
	    public static void main( String[] args ) throws Exception
	    {
	        Server server = new Server(8080);
	        server.setHandler(new HelloHandler());

	        server.start();
	        server.join();
	    }
	}

在 Jetty 中一个或者更多处理器处理所有请求。一些处理器选择其他特定的处理器（例如，ContextHandlerCollection 使用上下文路径选择 ContextHandler）；其他处理器使用应用逻辑生成响应（例如，ServletHandler 传送请求给应用 Servlet），同时其他处理器做跟生成响应无关的任务（例如，RequestLogHandler 或者 StatisticsHandler）。

后面的章节描述如何结合处理器方面的问题。你可以看到在 Jetty 的 [org.eclipse.jetty.server.handler](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/server/handler/package-summary.html) 包中有一些处理器。

#### Handler Collections and Wrappers

复杂的请求可以通过多种方式组合多个处理器进行处理。Jetty 有多个 [HandlerContainer](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/HandlerContainer.html) 接口的实现：

**[HandlerCollection](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/handler/HandlerCollection.html)**

  管理其他处理器的一个集合，并且按照顺序调用每个处理器。这对组合那些生成响应的处理器和统计数据、记录日志的处理器是有用的。

**[HandlerList](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/handler/HandlerList.html)**

  轮流调用每个处理器的处理器集合，直到抛出一个异常、提交了响应或者 request.isHandled() 返回 true。你可以用它组合根据条件处理一个请求的处理器，例如调用多个上下文直到匹配一个虚拟主机。

**[HandlerWrapper](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/handler/HandlerWrapper.html)**

  处理器的基础类，你可以在面向方面编程风格中一起使用菊花链处理器。例如，一个标准的 web 应用是通过一个 context、session、security和servlet处理器链实现的。

**[ContextHandlerCollection](http://download.eclipse.org/jetty/stable-9/apidocs/org/eclipse/jetty/server/handler/ContextHandlerCollection.html)**

  一个特定的 HandlerCollection，使用请求 URI 最长的前缀（contextPath）选择包含的 ContextHandler 来处理请求。

#### 处理器的作用域

很多 Jetty 中的标准 Servlet 容器是用 HandlerWrappers 实现的，是一个菊花式处理器链：ContextHandler 到 SessionHandler 到 SecurityHandler 到 ServletHandler。然而，因为 servlet 规范的特性，这个链不是一个处理器的纯粹嵌套，因为外部处理器有时需要内部处理器的过程信息。例如，当 ContextHandler 调用应用监听器来通知它们一个请求进入了上下文，它必须已经知道 ServletHandler 分发请求给哪个 servlet，因此 servletPath 方法返回正确的值。

HandlerWrapper 被定制为 [ScopedHandler](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/server/handler/ScopedHandler.html) 抽象类，它支持作用域的菊花链。例如，如果一个 ServletHandler 嵌套在一个 ContextHandler 中，方法的执行顺序和嵌套是：

Server.handle(...)
  ContextHandler.doScope(...)
    ServletHandler.doScope(...)
      ContextHandler.doHandle(...)
        ServletHandler.doHandle(...)
          SomeServlet.service(...)

因此，当 ContextHandler 处理请求的时候，它将在 ServletHandler 建立的作用域内进行。

#### Resource Handler

[FileServer](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/FileServer.html) 示例展示了如何使用 ResourceHandler 在当前的工作目录提供静态内容服务。

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Handler;
	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.handler.DefaultHandler;
	import org.eclipse.jetty.server.handler.HandlerList;
	import org.eclipse.jetty.server.handler.ResourceHandler;
	import org.eclipse.jetty.server.handler.gzip.GzipHandler;

	/**
	 * Simple Jetty FileServer.
	 * This is a simple example of Jetty configured as a FileServer.
	 */
	public class FileServer
	{
	    public static void main(String[] args) throws Exception
	    {
	        // Create a basic Jetty server object that will listen on port 8080.  Note that if you set this to port 0
	        // then a randomly available port will be assigned that you can either look in the logs for the port,
	        // or programmatically obtain it for use in test cases.
	        Server server = new Server(8080);

	        // Create the ResourceHandler. It is the object that will actually handle the request for a given file. It is
	        // a Jetty Handler object so it is suitable for chaining with other handlers as you will see in other examples.
	        ResourceHandler resource_handler = new ResourceHandler();
	        // Configure the ResourceHandler. Setting the resource base indicates where the files should be served out of.
	        // In this example it is the current directory but it can be configured to anything that the jvm has access to.
	        resource_handler.setDirectoriesListed(true);
	        resource_handler.setWelcomeFiles(new String[]{ "index.html" });
	        resource_handler.setResourceBase(".");

	        // Add the ResourceHandler to the server.
	        GzipHandler gzip = new GzipHandler();
	        server.setHandler(gzip);
	        HandlerList handlers = new HandlerList();
	        handlers.setHandlers(new Handler[] { resource_handler, new DefaultHandler() });
	        gzip.setHandler(handlers);

	        // Start things up! By using the server.join() the server thread will join with the current thread.
	        // See "http://docs.oracle.com/javase/1.5.0/docs/api/java/lang/Thread.html#join()" for more details.
	        server.start();
	        server.join();
	    }
	}

注意，ResourceHandler 和 DefaultHandler 使用了一个 HandlerList，因此 DefaultHandler 为所有找不到静态资源的请求生成良好的 404 响应。

### 嵌入式 Connectors

在前面的示例中，Server 实例传入了一个端口号，并且在内部创建一个在这个端口监听请求的 Connector 默认实例。然而，通常当嵌入 Jetty 的时候，需要明确实例化，并且给 Server 实例配置一个或多个 Connectors。

#### 一个 Connector

下面的示例，[OneConnector.java](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/OneConnector.html)，实例化、配置并添加一个 HTTP 连接器实例到服务器：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.ServerConnector;

	/**
	 * A Jetty server with one connectors.
	 */
	public class OneConnector
	{
	    public static void main( String[] args ) throws Exception
	    {
	        // The Server
	        Server server = new Server();

	        // HTTP connector
	        ServerConnector http = new ServerConnector(server);
	        http.setHost("localhost");
	        http.setPort(8080);
	        http.setIdleTimeout(30000);

	        // Set the connector
	        server.addConnector(http);

	        // Set a handler
	        server.setHandler(new HelloHandler());

	        // Start the server
	        server.start();
	        server.join();
	    }
	}

在这个示例中，连接器处理 HTTP 协议，这是 [ServerConnector](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/server/ServerConnector.html) 类默认的。

#### 多个 Connectors

当配置多个连接器（例如，HTTP 和 HTTPS）时，共享 HTTP 通用参数的配置是可取的。为了达到这个目的，你需要明确配置 ConnectionFactory 实例和 ServerConnector 类，并且跟 HTTP 通用配置一起提供。

[ManyConnectors 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/ManyConnectors.html) 配置了有两个 ServerConnector 实例的服务器：HTTP 连接器有一个 [HTTPConnectionFactory](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/server/HttpConnectionFactory.html) 实例；HTTPS 连接器有一个链接到 HttpConnectionFactory 的 SslConnectionFactory。两个 HttpConnectionFactories 在同一个 [HttpConfiguration](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/server/HttpConfiguration.html) 上配置，然而 HTTPS 工厂用一个包装的配置，所以可以添加 [SecureRequestCustomizer](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/server/SecureRequestCustomizer.html)。

### 嵌入 Servlets

[Servlets](https://en.wikipedia.org/wiki/Java_servlet) 是提供处理 HTTP 请求应用逻辑的一般方式。Servlets 跟 Jetty 的处理器是相同的，除非请求对象是不可变的，因此不能被修改。在 Jetty 中 Servlets 通过一个 [ServletHandler](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/MinimalServlets.html) 来处理。它使用标准的路径映射让 Servlet 和请求匹配；设置请求的 servletPath 和路径信息；传送请求到 servlet，可能通过 Filters 生成响应。

[MinimalServlets 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/MinimalServlets.html) 创建了一个 ServletHandler 实例并且配置了一个 HelloServlet:

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import java.io.IOException;

	import javax.servlet.ServletException;
	import javax.servlet.http.HttpServlet;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;

	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.servlet.ServletHandler;

	public class MinimalServlets
	{
	    public static void main( String[] args ) throws Exception
	    {
	        // Create a basic jetty server object that will listen on port 8080.
	        // Note that if you set this to port 0 then a randomly available port
	        // will be assigned that you can either look in the logs for the port,
	        // or programmatically obtain it for use in test cases.
	        Server server = new Server(8080);

	        // The ServletHandler is a dead simple way to create a context handler
	        // that is backed by an instance of a Servlet.
	        // This handler then needs to be registered with the Server object.
	        ServletHandler handler = new ServletHandler();
	        server.setHandler(handler);

	        // Passing in the class for the Servlet allows jetty to instantiate an
	        // instance of that Servlet and mount it on a given context path.

	        // IMPORTANT:
	        // This is a raw Servlet, not a Servlet that has been configured
	        // through a web.xml @WebServlet annotation, or anything similar.
	        handler.addServletWithMapping(HelloServlet.class, "/*");

	        // Start things up!
	        server.start();

	        // The use of server.join() the will make the current thread join and
	        // wait until the server is done executing.
	        // See
	        // http://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html#join()
	        server.join();
	    }

	    @SuppressWarnings("serial")
	    public static class HelloServlet extends HttpServlet
	    {
	        @Override
	        protected void doGet( HttpServletRequest request,
	                              HttpServletResponse response ) throws ServletException,
	                                                            IOException
	        {
	            response.setContentType("text/html");
	            response.setStatus(HttpServletResponse.SC_OK);
	            response.getWriter().println("<h1>Hello from HelloServlet</h1>");
	        }
	    }
	}

### 嵌入 Contexts

[ContextHandler](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/OneContext.html) 是一个 ScopedHandler 只来响应那些 URI 前缀匹配已配置的上下文路径的请求。匹配上下文路径的请求会响应的调整它们的路径方法，并且上下文生命周期内是可获取的，可能包含：

- 当请求在作用域内处理时，Classloader 设置为线程上下文的类加载器。
- 通过过 [ServletContext](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContext.html) API 可以获取属性的集合。
- 通过过 [ServletContext](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContext.html) API 可以获取参数的集合。
- 通过过 [ServletContext](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContext.html) API 为静态资源的请求提供一个基本的 Resource 作为文档根。
- 虚拟主机名称集合。

下面的 [OneContext 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/OneContext.html) 建立了一个包装了 [HelloHandler](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/HelloHandler.html) 的上下文：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.handler.ContextHandler;

	public class OneContext
	{
	    public static void main( String[] args ) throws Exception
	    {
	        Server server = new Server( 8080 );

	        // Add a single handler on context "/hello"
	        ContextHandler context = new ContextHandler();
	        context.setContextPath( "/hello" );
	        context.setHandler( new HelloHandler() );

	        // Can be accessed using http://localhost:8080/hello

	        server.setHandler( context );

	        // Start the server
	        server.start();
	        server.join();
	    }
	}

当有很多上下文出现时，你可以嵌入一个 ContextHandlerCollection 来有效的测试一个请求的 URI，然后选择匹配到的 ContextHandler。[ManyContexts 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/ManyContexts.html) 展示了可以配置多个上下文：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Handler;
	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.handler.ContextHandler;
	import org.eclipse.jetty.server.handler.ContextHandlerCollection;

	public class ManyContexts
	{
	    public static void main( String[] args ) throws Exception
	    {
	        Server server = new Server(8080);

	        ContextHandler context = new ContextHandler("/");
	        context.setContextPath("/");
	        context.setHandler(new HelloHandler("Root Hello"));

	        ContextHandler contextFR = new ContextHandler("/fr");
	        contextFR.setHandler(new HelloHandler("Bonjoir"));

	        ContextHandler contextIT = new ContextHandler("/it");
	        contextIT.setHandler(new HelloHandler("Bongiorno"));

	        ContextHandler contextV = new ContextHandler("/");
	        contextV.setVirtualHosts(new String[] { "127.0.0.2" });
	        contextV.setHandler(new HelloHandler("Virtual Hello"));

	        ContextHandlerCollection contexts = new ContextHandlerCollection();
	        contexts.setHandlers(new Handler[] { context, contextFR, contextIT,
	                contextV });

	        server.setHandler(contexts);

	        server.start();
	        server.join();
	    }
	}

### 嵌入 ServletContexts

[ServletContextHandler](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/servlet/ServletContextHandler.html) 是一个支持普通 session 和 Servlet 的特定的 ContextHandler。下面的 [OneServletContext 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/OneServletContext.html) 实例化了一个 [DefaultServlet](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/servlet/DefaultServlet.html) 从 /tmp/ 提供静态内容，并且实例化一个 DumpServlet 创建 session 并存储请求的基本详细信息：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.servlet.DefaultServlet;
	import org.eclipse.jetty.servlet.ServletContextHandler;

	public class OneServletContext
	{
	    public static void main( String[] args ) throws Exception
	    {
	        Server server = new Server(8080);

	        ServletContextHandler context = new ServletContextHandler(
	                ServletContextHandler.SESSIONS);
	        context.setContextPath("/");
	        context.setResourceBase(System.getProperty("java.io.tmpdir"));
	        server.setHandler(context);

	        // Add dump servlet
	        context.addServlet(DumpServlet.class, "/dump/*");
	        // Add default servlet
	        context.addServlet(DefaultServlet.class, "/");

	        server.start();
	        server.join();
	    }
	}

### 嵌入 Web Applications

[WebAppContext](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/webapp/WebAppContext.html) 是一个 ServletContextHandler 的扩展，它使用[标准设计](https://en.wikipedia.org/wiki/WAR_(file_format))和 web.xml 配置 servlet、filter 和 web.xml 或注解的其他特性。下面的 [OneWebApp 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/OneWebApp.html) 配置了 Jetty 的测试 web 应用。Web 应用程序可以使用容器提供的的资源，在这个案例中需要并配置一个 LoginService：

	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import java.io.File;
	import java.lang.management.ManagementFactory;

	import org.eclipse.jetty.jmx.MBeanContainer;
	import org.eclipse.jetty.security.HashLoginService;
	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.handler.AllowSymLinkAliasChecker;
	import org.eclipse.jetty.webapp.WebAppContext;

	public class OneWebApp
	{
	    public static void main( String[] args ) throws Exception
	    {
	        // Create a basic jetty server object that will listen on port 8080.
	        // Note that if you set this to port 0 then a randomly available port
	        // will be assigned that you can either look in the logs for the port,
	        // or programmatically obtain it for use in test cases.
	        Server server = new Server(8080);

	        // Setup JMX
	        MBeanContainer mbContainer = new MBeanContainer(
	                ManagementFactory.getPlatformMBeanServer());
	        server.addBean(mbContainer);

	        // The WebAppContext is the entity that controls the environment in
	        // which a web application lives and breathes. In this example the
	        // context path is being set to "/" so it is suitable for serving root
	        // context requests and then we see it setting the location of the war.
	        // A whole host of other configurations are available, ranging from
	        // configuring to support annotation scanning in the webapp (through
	        // PlusConfiguration) to choosing where the webapp will unpack itself.
	        WebAppContext webapp = new WebAppContext();
	        webapp.setContextPath("/");
	        File warFile = new File(
	                "../../tests/test-jmx/jmx-webapp/target/jmx-webapp");
	        webapp.setWar(warFile.getAbsolutePath());

	        // A WebAppContext is a ContextHandler as well so it needs to be set to
	        // the server so it is aware of where to send the appropriate requests.
	        server.setHandler(webapp);

	        // Start things up!
	        server.start();

	        // The use of server.join() the will make the current thread join and
	        // wait until the server is done executing.
	        // See http://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html#join()
	        server.join();
	    }
	}

### 喜欢 Jetty XML

配置 Jetty 服务器实例的典型方式是通过 jetty.xml 关联配置文件。然而 Jetty XML 配置格式只是在代码中可以做的简单的表达；也可以很简单的写嵌入代码准确实现 jetty.xml 配置。下面的 [LikeJettyXml 示例](http://download.eclipse.org/jetty/stable-9/xref/org/eclipse/jetty/embedded/LikeJettyXml.html) 在代码中呈现了从配置文件获取的行为：

- jetty.xml
- jetty-jmx.xml
- jetty-http.xml
- jetty-https.xml
- jetty-deploy.xml
- jetty-stats.xml
- jetty-requestlog.xml
- jetty-lowresources.xml
- test-realm.xml


	//
	//  ========================================================================
	//  Copyright (c) 1995-2016 Mort Bay Consulting Pty. Ltd.
	//  ------------------------------------------------------------------------
	//  All rights reserved. This program and the accompanying materials
	//  are made available under the terms of the Eclipse Public License v1.0
	//  and Apache License v2.0 which accompanies this distribution.
	//
	//      The Eclipse Public License is available at
	//      http://www.eclipse.org/legal/epl-v10.html
	//
	//      The Apache License v2.0 is available at
	//      http://www.opensource.org/licenses/apache2.0.php
	//
	//  You may elect to redistribute this code under either of these licenses.
	//  ========================================================================
	//

	package org.eclipse.jetty.embedded;

	import java.io.File;
	import java.io.FileNotFoundException;
	import java.lang.management.ManagementFactory;

	import org.eclipse.jetty.deploy.DeploymentManager;
	import org.eclipse.jetty.deploy.PropertiesConfigurationManager;
	import org.eclipse.jetty.deploy.bindings.DebugListenerBinding;
	import org.eclipse.jetty.deploy.providers.WebAppProvider;
	import org.eclipse.jetty.http.HttpVersion;
	import org.eclipse.jetty.jmx.MBeanContainer;
	import org.eclipse.jetty.security.HashLoginService;
	import org.eclipse.jetty.server.DebugListener;
	import org.eclipse.jetty.server.Handler;
	import org.eclipse.jetty.server.HttpConfiguration;
	import org.eclipse.jetty.server.HttpConnectionFactory;
	import org.eclipse.jetty.server.LowResourceMonitor;
	import org.eclipse.jetty.server.NCSARequestLog;
	import org.eclipse.jetty.server.SecureRequestCustomizer;
	import org.eclipse.jetty.server.Server;
	import org.eclipse.jetty.server.ServerConnector;
	import org.eclipse.jetty.server.SslConnectionFactory;
	import org.eclipse.jetty.server.handler.ContextHandlerCollection;
	import org.eclipse.jetty.server.handler.DefaultHandler;
	import org.eclipse.jetty.server.handler.HandlerCollection;
	import org.eclipse.jetty.server.handler.RequestLogHandler;
	import org.eclipse.jetty.server.handler.StatisticsHandler;
	import org.eclipse.jetty.util.ssl.SslContextFactory;
	import org.eclipse.jetty.util.thread.QueuedThreadPool;
	import org.eclipse.jetty.util.thread.ScheduledExecutorScheduler;
	import org.eclipse.jetty.webapp.Configuration;

	/**
	 * Starts the Jetty Distribution's demo-base directory using entirely
	 * embedded jetty techniques.
	 */
	public class LikeJettyXml
	{
	    public static void main( String[] args ) throws Exception
	    {
	        // Path to as-built jetty-distribution directory
	        String jettyHomeBuild = "../../jetty-distribution/target/distribution";

	        // Find jetty home and base directories
	        String homePath = System.getProperty("jetty.home", jettyHomeBuild);
	        File homeDir = new File(homePath);
	        if (!homeDir.exists())
	        {
	            throw new FileNotFoundException(homeDir.getAbsolutePath());
	        }
	        String basePath = System.getProperty("jetty.base", homeDir + "/demo-base");
	        File baseDir = new File(basePath);
	        if(!baseDir.exists())
	        {
	            throw new FileNotFoundException(baseDir.getAbsolutePath());
	        }

	        // Configure jetty.home and jetty.base system properties
	        String jetty_home = homeDir.getAbsolutePath();
	        String jetty_base = baseDir.getAbsolutePath();
	        System.setProperty("jetty.home", jetty_home);
	        System.setProperty("jetty.base", jetty_base);


	        // === jetty.xml ===
	        // Setup Threadpool
	        QueuedThreadPool threadPool = new QueuedThreadPool();
	        threadPool.setMaxThreads(500);

	        // Server
	        Server server = new Server(threadPool);

	        // Scheduler
	        server.addBean(new ScheduledExecutorScheduler());

	        // HTTP Configuration
	        HttpConfiguration http_config = new HttpConfiguration();
	        http_config.setSecureScheme("https");
	        http_config.setSecurePort(8443);
	        http_config.setOutputBufferSize(32768);
	        http_config.setRequestHeaderSize(8192);
	        http_config.setResponseHeaderSize(8192);
	        http_config.setSendServerVersion(true);
	        http_config.setSendDateHeader(false);
	        // httpConfig.addCustomizer(new ForwardedRequestCustomizer());

	        // Handler Structure
	        HandlerCollection handlers = new HandlerCollection();
	        ContextHandlerCollection contexts = new ContextHandlerCollection();
	        handlers.setHandlers(new Handler[] { contexts, new DefaultHandler() });
	        server.setHandler(handlers);

	        // Extra options
	        server.setDumpAfterStart(false);
	        server.setDumpBeforeStop(false);
	        server.setStopAtShutdown(true);

	        // === jetty-jmx.xml ===
	        MBeanContainer mbContainer = new MBeanContainer(
	                ManagementFactory.getPlatformMBeanServer());
	        server.addBean(mbContainer);


	        // === jetty-http.xml ===
	        ServerConnector http = new ServerConnector(server,
	                new HttpConnectionFactory(http_config));
	        http.setPort(8080);
	        http.setIdleTimeout(30000);
	        server.addConnector(http);


	        // === jetty-https.xml ===
	        // SSL Context Factory
	        SslContextFactory sslContextFactory = new SslContextFactory();
	        sslContextFactory.setKeyStorePath(jetty_home + "/../../../jetty-server/src/test/config/etc/keystore");
	        sslContextFactory.setKeyStorePassword("OBF:1vny1zlo1x8e1vnw1vn61x8g1zlu1vn4");
	        sslContextFactory.setKeyManagerPassword("OBF:1u2u1wml1z7s1z7a1wnl1u2g");
	        sslContextFactory.setTrustStorePath(jetty_home + "/../../../jetty-server/src/test/config/etc/keystore");
	        sslContextFactory.setTrustStorePassword("OBF:1vny1zlo1x8e1vnw1vn61x8g1zlu1vn4");
	        sslContextFactory.setExcludeCipherSuites("SSL_RSA_WITH_DES_CBC_SHA",
	                "SSL_DHE_RSA_WITH_DES_CBC_SHA", "SSL_DHE_DSS_WITH_DES_CBC_SHA",
	                "SSL_RSA_EXPORT_WITH_RC4_40_MD5",
	                "SSL_RSA_EXPORT_WITH_DES40_CBC_SHA",
	                "SSL_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA",
	                "SSL_DHE_DSS_EXPORT_WITH_DES40_CBC_SHA");

	        // SSL HTTP Configuration
	        HttpConfiguration https_config = new HttpConfiguration(http_config);
	        https_config.addCustomizer(new SecureRequestCustomizer());

	        // SSL Connector
	        ServerConnector sslConnector = new ServerConnector(server,
	            new SslConnectionFactory(sslContextFactory,HttpVersion.HTTP_1_1.asString()),
	            new HttpConnectionFactory(https_config));
	        sslConnector.setPort(8443);
	        server.addConnector(sslConnector);


	        // === jetty-deploy.xml ===
	        DeploymentManager deployer = new DeploymentManager();
	        DebugListener debug = new DebugListener(System.out,true,true,true);
	        server.addBean(debug);        
	        deployer.addLifeCycleBinding(new DebugListenerBinding(debug));
	        deployer.setContexts(contexts);
	        deployer.setContextAttribute(
	                "org.eclipse.jetty.server.webapp.ContainerIncludeJarPattern",
	                ".*/servlet-api-[^/]*\\.jar$");

	        WebAppProvider webapp_provider = new WebAppProvider();
	        webapp_provider.setMonitoredDirName(jetty_base + "/webapps");
	        webapp_provider.setDefaultsDescriptor(jetty_home + "/etc/webdefault.xml");
	        webapp_provider.setScanInterval(1);
	        webapp_provider.setExtractWars(true);
	        webapp_provider.setConfigurationManager(new PropertiesConfigurationManager());

	        deployer.addAppProvider(webapp_provider);
	        server.addBean(deployer);

	        // === setup jetty plus ==
	        Configuration.ClassList.setServerDefault(server).addAfter(
	                "org.eclipse.jetty.webapp.FragmentConfiguration",
	                "org.eclipse.jetty.plus.webapp.EnvConfiguration",
	                "org.eclipse.jetty.plus.webapp.PlusConfiguration");

	        // === jetty-stats.xml ===
	        StatisticsHandler stats = new StatisticsHandler();
	        stats.setHandler(server.getHandler());
	        server.setHandler(stats);


	        // === jetty-requestlog.xml ===
	        NCSARequestLog requestLog = new NCSARequestLog();
	        requestLog.setFilename(jetty_home + "/logs/yyyy_mm_dd.request.log");
	        requestLog.setFilenameDateFormat("yyyy_MM_dd");
	        requestLog.setRetainDays(90);
	        requestLog.setAppend(true);
	        requestLog.setExtended(true);
	        requestLog.setLogCookies(false);
	        requestLog.setLogTimeZone("GMT");
	        RequestLogHandler requestLogHandler = new RequestLogHandler();
	        requestLogHandler.setRequestLog(requestLog);
	        handlers.addHandler(requestLogHandler);


	        // === jetty-lowresources.xml ===
	        LowResourceMonitor lowResourcesMonitor=new LowResourceMonitor(server);
	        lowResourcesMonitor.setPeriod(1000);
	        lowResourcesMonitor.setLowResourcesIdleTimeout(200);
	        lowResourcesMonitor.setMonitorThreads(true);
	        lowResourcesMonitor.setMaxConnections(0);
	        lowResourcesMonitor.setMaxMemory(0);
	        lowResourcesMonitor.setMaxLowResourcesTime(5000);
	        server.addBean(lowResourcesMonitor);


	        // === test-realm.xml ===
	        HashLoginService login = new HashLoginService();
	        login.setName("Test Realm");
	        login.setConfig(jetty_base + "/etc/realm.properties");
	        login.setHotReload(false);
	        server.addBean(login);

	        // Start the server
	        server.start();
	        server.join();
	    }
	}
