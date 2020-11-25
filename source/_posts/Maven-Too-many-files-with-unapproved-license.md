title: 'Maven: Too many files with unapproved license'
date: 2020-11-25 15:15:35
tags:
- Maven
- Java
categories:
- 开发工具
- Maven
---

异常信息如下：

	[ERROR] Failed to execute goal org.apache.rat:apache-rat-plugin:0.11:check (default) on project ranger: Too many files with unapproved license: 1 See RAT report in: /home/zhangjc/ysten/git/apache-ranger-1.2.0/target/rat.txt -> [Help 1]
	org.apache.maven.lifecycle.LifecycleExecutionException: Failed to execute goal org.apache.rat:apache-rat-plugin:0.11:check (default) on project ranger: Too many files with unapproved license: 1 See RAT report in: /home/zhangjc/ysten/git/apache-ranger-1.2.0/target/rat.txt
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:215)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:156)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:148)
	    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:117)
	    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:81)
	    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:56)
	    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:128)
	    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:305)
	    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:192)
	    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:105)
	    at org.apache.maven.cli.MavenCli.execute (MavenCli.java:957)
	    at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:289)
	    at org.apache.maven.cli.MavenCli.main (MavenCli.java:193)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:282)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:225)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:406)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:347)
	   
解决方法：增加 -Drat.skip=true 参数 ，跳过licensing 检查。