title: Hadoop2.7.3 编译错误一例
date: 2021-01-26 16:39:01
tags:
- Hadoop
- 大数据
categories:
- 大数据
- Hadoop
---

错误信息如下：

	[ERROR] Failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (dist) on project hadoop-kms: An Ant BuildException has occured: exec returned: 2
	[ERROR] around Ant part ...<exec failonerror="true" dir="/data/jenkins/.jenkins/workspace/HttpFSProduct/hadoop-common-project/hadoop-kms/target" executable="sh">... @ 10:137 in /data/jenkins/.jenkins/workspace/HttpFSProduct/hadoop-common-project/hadoop-kms/target/antrun/build-main.xml
	[ERROR] -> [Help 1]
	org.apache.maven.lifecycle.LifecycleExecutionException: Failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (dist) on project hadoop-kms: An Ant BuildException has occured: exec returned: 2
	around Ant part ...<exec failonerror="true" dir="/data/jenkins/.jenkins/workspace/HttpFSProduct/hadoop-common-project/hadoop-kms/target" executable="sh">... @ 10:137 in /data/jenkins/.jenkins/workspace/HttpFSProduct/hadoop-common-project/hadoop-kms/target/antrun/build-main.xml
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
	    at org.jvnet.hudson.maven3.launcher.Maven35Launcher.main (Maven35Launcher.java:130)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:282)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:225)
	    at jenkins.maven3.agent.Maven35Main.launch (Maven35Main.java:176)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at hudson.maven.Maven3Builder.call (Maven3Builder.java:139)
	    at hudson.maven.Maven3Builder.call (Maven3Builder.java:70)
	    at hudson.remoting.UserRequest.perform (UserRequest.java:212)
	    at hudson.remoting.UserRequest.perform (UserRequest.java:54)
	    at hudson.remoting.Request$2.run (Request.java:369)
	    at hudson.remoting.InterceptingExecutorService$1.call (InterceptingExecutorService.java:72)
	    at java.util.concurrent.FutureTask.run (FutureTask.java:266)
	    at java.util.concurrent.ThreadPoolExecutor.runWorker (ThreadPoolExecutor.java:1149)
	    at java.util.concurrent.ThreadPoolExecutor$Worker.run (ThreadPoolExecutor.java:624)
	    at java.lang.Thread.run (Thread.java:748)
	Caused by: org.apache.maven.plugin.MojoExecutionException: An Ant BuildException has occured: exec returned: 2
	around Ant part ...<exec failonerror="true" dir="/data/jenkins/.jenkins/workspace/HttpFSProduct/hadoop-common-project/hadoop-kms/target" executable="sh">... @ 10:137 in /data/jenkins/.jenkins/workspace/HttpFSProduct/hadoop-common-project/hadoop-kms/target/antrun/build-main.xml
	    at org.apache.maven.plugin.antrun.AntRunMojo.execute (AntRunMojo.java:355)
	    at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:137)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:210)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:156)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:148)
	    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:117)
	    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:81)
	    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:56)
	    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:128)
	    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:305)
	    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:192)
	    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:105)
	    at org.jvnet.hudson.maven3.launcher.Maven35Launcher.main (Maven35Launcher.java:130)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:282)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:225)
	    at jenkins.maven3.agent.Maven35Main.launch (Maven35Main.java:176)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at hudson.maven.Maven3Builder.call (Maven3Builder.java:139)
	    at hudson.maven.Maven3Builder.call (Maven3Builder.java:70)
	    at hudson.remoting.UserRequest.perform (UserRequest.java:212)
	    at hudson.remoting.UserRequest.perform (UserRequest.java:54)
	    at hudson.remoting.Request$2.run (Request.java:369)
	    at hudson.remoting.InterceptingExecutorService$1.call (InterceptingExecutorService.java:72)
	    at java.util.concurrent.FutureTask.run (FutureTask.java:266)
	    at java.util.concurrent.ThreadPoolExecutor.runWorker (ThreadPoolExecutor.java:1149)
	    at java.util.concurrent.ThreadPoolExecutor$Worker.run (ThreadPoolExecutor.java:624)
	    at java.lang.Thread.run (Thread.java:748)
	Caused by: org.apache.tools.ant.BuildException: exec returned: 2
	    at org.apache.tools.ant.taskdefs.ExecTask.runExecute (ExecTask.java:646)
	    at org.apache.tools.ant.taskdefs.ExecTask.runExec (ExecTask.java:672)
	    at org.apache.tools.ant.taskdefs.ExecTask.execute (ExecTask.java:498)
	    at org.apache.tools.ant.UnknownElement.execute (UnknownElement.java:291)
	    at sun.reflect.GeneratedMethodAccessor37.invoke (Unknown Source)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at org.apache.tools.ant.dispatch.DispatchUtils.execute (DispatchUtils.java:106)
	    at org.apache.tools.ant.Task.perform (Task.java:348)
	    at org.apache.tools.ant.Target.execute (Target.java:390)
	    at org.apache.tools.ant.Target.performTasks (Target.java:411)
	    at org.apache.tools.ant.Project.executeSortedTargets (Project.java:1399)
	    at org.apache.tools.ant.Project.executeTarget (Project.java:1368)
	    at org.apache.maven.plugin.antrun.AntRunMojo.execute (AntRunMojo.java:327)
	    at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:137)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:210)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:156)
	    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:148)
	    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:117)
	    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:81)
	    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:56)
	    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:128)
	    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:305)
	    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:192)
	    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:105)
	    at org.jvnet.hudson.maven3.launcher.Maven35Launcher.main (Maven35Launcher.java:130)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:282)
	    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:225)
	    at jenkins.maven3.agent.Maven35Main.launch (Maven35Main.java:176)
	    at sun.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
	    at sun.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:62)
	    at sun.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
	    at java.lang.reflect.Method.invoke (Method.java:498)
	    at hudson.maven.Maven3Builder.call (Maven3Builder.java:139)
	    at hudson.maven.Maven3Builder.call (Maven3Builder.java:70)
	    at hudson.remoting.UserRequest.perform (UserRequest.java:212)
	    at hudson.remoting.UserRequest.perform (UserRequest.java:54)
	    at hudson.remoting.Request$2.run (Request.java:369)
	    at hudson.remoting.InterceptingExecutorService$1.call (InterceptingExecutorService.java:72)
	    at java.util.concurrent.FutureTask.run (FutureTask.java:266)
	    at java.util.concurrent.ThreadPoolExecutor.runWorker (ThreadPoolExecutor.java:1149)
	    at java.util.concurrent.ThreadPoolExecutor$Worker.run (ThreadPoolExecutor.java:624)
	    at java.lang.Thread.run (Thread.java:748)
	[ERROR] 
	[ERROR] Re-run Maven using the -X switch to enable full debug logging.
	[ERROR] 
	[ERROR] For more information about the errors and possible solutions, please read the following articles:
	[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
	[ERROR] 
	[ERROR] After correcting the problems, you can resume the build with the command
	[ERROR]   mvn <goals> -rf :hadoop-kms

百度后发现是 tomcat 下载不完整导致的，只下载了2M。如下：

	# pwd
	/data/hadoop2.7.3/hadoop-common-project/hadoop-kms/downloads
	# ll
	总用量 1988
	-rw-rw-r-- 1 jenkins jenkins 2035659 1月  26 16:07 apache-tomcat-6.0.44.tar.gz
	# du -sh *
	2.0M	apache-tomcat-6.0.44.tar.gz
	
下载完整的包并放到上面的目录后重试解决。