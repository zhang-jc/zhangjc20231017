title: Java Socket 多线程实例
tags:
  - Java
  - Socket
  - 多线程
categories:
  - 语言
  - Java
date: 2016-06-02 23:10:22
---

### 概述
Java 网络编程的客户端和服务端编写都非常简单。客户端使用 Socket 对象与服务端交互；服务端创建 ServerSocket 对象，然后使用accept()方法进行监听，并返回一个Socket的对象。accept 方法是阻塞调用，并将一直阻塞，直到接收到下一个客户端请求为止。非多线程情况下，服务器同时只能服务一个客户端。这通常是不能满足现实需求的。

<!-- more -->

对于现实场景是通过在服务端创建多个线程来服务多个客户端，如下图：
![Java Socket 多线程示意图](/uploads/20160602/Socket.png)

### 服务端

    import java.io.IOException;
    import java.io.InputStream;
    import java.io.OutputStream;
    import java.net.ServerSocket;
    import java.net.Socket;
    import java.net.SocketException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    public class EchoMultiServer {
	    private static ServerSocket server = null;
	    private static ExecutorService threadPool;

	    public static void main(String[] args) {
		    threadPool = Executors.newCachedThreadPool();
		    threadPool.submit(new Monitor());

		    try {
			    server = new ServerSocket(10000);

			    System.out.println("Server listening on port 10000 ....");
			    System.out.println("Hit Enter to stop the server");

			    while (true) {
				    Socket socket = server.accept();
				    System.out.println("Thread created");
				    threadPool.submit(new EchoThread(socket));
			    }
		    } catch (SocketException e) {
			    System.out.println("Server is down");
		    } catch (IOException e) {
			    e.printStackTrace();
		    }
	    }

	    private static void shutdownServer() {
		    try {
			    server.close();
		    } catch (IOException e) {
			    e.printStackTrace();
		    }

		    threadPool.shutdown();
		    System.exit(0);
	    }

	    private static class Monitor implements Runnable {

		    @Override
		    public void run() {
			    try {
				    while (System.in.read() != '\n') {}
			    } catch (IOException e) {
			    }

			    shutdownServer();
		    }
	    }
    }

    class EchoThread implements Runnable {
	    private Socket socket = null;
	    private byte[] buffer = new byte[512];

	    public EchoThread(Socket socket) {
		    this.socket = socket;
	    }

	    @Override
	    public void run() {
		    try {
			    InputStream is = socket.getInputStream();
			    is.read(buffer);
			    OutputStream os = socket.getOutputStream();
			    os.write(buffer);
		    } catch (IOException e) {
			    e.printStackTrace();
		    } finally {
			    try {
				    socket.close();
			    } catch (IOException e) {
				    e.printStackTrace();
			    }
		    }
	    }
    }

### 客户端

    import java.io.InputStream;
    import java.io.OutputStream;
    import java.net.InetSocketAddress;
    import java.net.Socket;
    import java.util.Scanner;

    public class EchoMultiClient {
	    private static int counter;

	    public static void main(String[] args) {
		    for (int i = 0; i < 100; i++) {
			    counter++;
			    new Thread(new Client(counter)).start();
		    }
	    }

	    private static class Client implements Runnable {
		    private int counter;

		    public Client(int counter) {
			    this.counter = counter;
		    }

		    @Override
		    public void run() {
			    try {
				    Socket socket = new Socket();
				    socket.connect(new InetSocketAddress("localhost", 10000), 1000);

				    try {
					    OutputStream os = socket.getOutputStream();
					    String str = "Hello from Client " + counter;
					    os.write(str.getBytes());
					    InputStream is = socket.getInputStream();
					    Scanner scanner = new Scanner(is);
					    while (scanner.hasNextLine()) {
						    System.out.println(scanner.nextLine());
					    }

					    Thread.sleep(10);
					    scanner.close();
				    } finally {
					    socket.close();
				    }
			    } catch (Exception e) {
				    e.printStackTrace();
			    }
		    }
	    }
    }
