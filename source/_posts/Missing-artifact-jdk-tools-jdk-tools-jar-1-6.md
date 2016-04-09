title: 'Missing artifact jdk.tools:jdk.tools:jar:1.6'
date: 2015-11-17 14:06:14
tags: maven eclipse jdk
categories: 
- 开发工具
- maven
---
Eclipse maven工程 Missing artifact jdk.tools:jdk.tools:jar:1.6 可以用以下方法解决。思路是配置 maven 依赖本地的 tools.jar ，需要本地安装有 jdk 1.6 。
### 下载安装JDK 1.6
Oracle 官网下载JDK 1.6 的链接不太容易找到，可以在 [Java SE Site Map](http://www.oracle.com/technetwork/java/javase/sitemap-jsp-139155.html) 页面找到以下位置，点击即可看到 JDK 所有版本的列表：

![java se site map](/uploads/java-se-site-map.png)

### 配置 maven 依赖

	<dependency>
		<groupId>jdk.tools</groupId>
		<artifactId>jdk.tools</artifactId>
		<version>1.6</version>
		<scope>system</scope>
		<systemPath>${JAVA_HOME}/lib/tools.jar</systemPath>
	</dependency-->

注意，如果使用 ${JAVA_HOME} 配置，系统环境变量必须要配置 JAVA_HOME 。也可以直接使用本地路径，如下：

	<dependency>
		<groupId>jdk.tools</groupId>
		<artifactId>jdk.tools</artifactId>
		<version>1.6</version>
		<scope>system</scope>
		<systemPath>C:/Program Files/Java/jdk1.6.0_45/lib/tools.jar</systemPath>
	</dependency>
