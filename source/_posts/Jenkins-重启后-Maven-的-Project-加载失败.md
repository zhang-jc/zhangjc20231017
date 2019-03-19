title: Jenkins 重启后 Maven 的 Project 加载失败
date: 2019-03-19 15:23:05
tags:
- Jenkins
categories:
- 开发工具
- Jenkins
---
Jenkins 重启后发现 Maven 的项目都没有正常加载。检查 Jenkins 的启动日志发现以下错误信息：

    java.io.IOException: Unable to read /home/jenkins/.jenkins/jobs/test-maven/config.xml
    	at hudson.XmlFile.read(XmlFile.java:149)
    	at hudson.model.Items.load(Items.java:371)
    	at jenkins.model.Jenkins$14.run(Jenkins.java:3128)
    	at org.jvnet.hudson.reactor.TaskGraphBuilder$TaskImpl.run(TaskGraphBuilder.java:169)
    	at org.jvnet.hudson.reactor.Reactor.runTask(Reactor.java:296)
    	at jenkins.model.Jenkins$5.runTask(Jenkins.java:1069)
    	at org.jvnet.hudson.reactor.Reactor$2.run(Reactor.java:214)
    	at org.jvnet.hudson.reactor.Reactor$Node.run(Reactor.java:117)
    	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
    	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
    	at java.lang.Thread.run(Thread.java:748)
    Caused by: com.thoughtworks.xstream.mapper.CannotResolveClassException: maven2-moduleset
    	at com.thoughtworks.xstream.mapper.DefaultMapper.realClass(DefaultMapper.java:79)
    	at com.thoughtworks.xstream.mapper.MapperWrapper.realClass(MapperWrapper.java:30)
    	at com.thoughtworks.xstream.mapper.DynamicProxyMapper.realClass(DynamicProxyMapper.java:55)
    	at com.thoughtworks.xstream.mapper.MapperWrapper.realClass(MapperWrapper.java:30)
    	at com.thoughtworks.xstream.mapper.PackageAliasingMapper.realClass(PackageAliasingMapper.java:88)
    	at com.thoughtworks.xstream.mapper.MapperWrapper.realClass(MapperWrapper.java:30)

将 Maven Integration plugin 重装并重启 Jenkins 后恢复正常。
