---
title: 'Docker容器：Can''t attach to the process: ptrace(PTRACE_ATTACH, ..) failed for XX'
date: 2021-10-06 00:02:48
tags:
- Docker
- Java
- JVM
---

# 异常信息

在Docker容器中使用jmap查看JVM内存时出现以下错误：

```Shell
[root@ebd4ba8d5b72]# ps -ef|grep java
root        18     1  0 23:50 pts/0    00:00:00 java -jar math-game.jar
root       136    69  0 23:52 pts/1    00:00:00 grep --color=auto java
[root@ebd4ba8d5b72]# jmap -heap 18
Attaching to process ID 18, please wait...
ERROR: ptrace(PTRACE_ATTACH, ..) failed for 18: Operation not permitted
Error attaching to process: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 18: Operation not permitted
sun.jvm.hotspot.debugger.DebuggerException: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 18: Operation not permitted
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal$LinuxDebuggerLocalWorkerThread.execute(LinuxDebuggerLocal.java:163)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.attach(LinuxDebuggerLocal.java:278)
	at sun.jvm.hotspot.HotSpotAgent.attachDebugger(HotSpotAgent.java:671)
	at sun.jvm.hotspot.HotSpotAgent.setupDebuggerLinux(HotSpotAgent.java:611)
	at sun.jvm.hotspot.HotSpotAgent.setupDebugger(HotSpotAgent.java:337)
	at sun.jvm.hotspot.HotSpotAgent.go(HotSpotAgent.java:304)
	at sun.jvm.hotspot.HotSpotAgent.attach(HotSpotAgent.java:140)
	at sun.jvm.hotspot.tools.Tool.start(Tool.java:185)
	at sun.jvm.hotspot.tools.Tool.execute(Tool.java:118)
	at sun.jvm.hotspot.tools.HeapSummary.main(HeapSummary.java:49)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at sun.tools.jmap.JMap.runTool(JMap.java:201)
	at sun.tools.jmap.JMap.main(JMap.java:130)
Caused by: sun.jvm.hotspot.debugger.DebuggerException: Can't attach to the process: ptrace(PTRACE_ATTACH, ..) failed for 18: Operation not permitted
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.attach0(Native Method)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal.access$100(LinuxDebuggerLocal.java:62)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal$1AttachTask.doit(LinuxDebuggerLocal.java:269)
	at sun.jvm.hotspot.debugger.linux.LinuxDebuggerLocal$LinuxDebuggerLocalWorkerThread.run(LinuxDebuggerLocal.java:138)
```

# 方法一（已验证Ok）

在容器启动时添加--cap-add=SYS_PTRACE，如下：

```Shell
docker run --cap-add=SYS_PTRACE -v /root:/root -it 7a4460957494 bash
```

这种方式在调试镜像时没问题。但是在线上环境会存在比较严重的安全问题。

# 方法二（未能验证）

方法二是修改Linux内核设置。执行以下命令：

```Shell
echo 0 > /proc/sys/kernel/yama/ptrace_scope
```

或者在配置文件/etc/sysctl.d/10-ptrace.conf中修改或添加以下配置：

```INI
kernel.yama.ptrace_scope = 0
```

在我的环境中两种方式都会报以下错误信息：

```Log
/proc/sys/kernel/yama/ptrace_scope: No such file or directory
```

具体原因我没有深究，怀疑跟我的操作系统版本有关：

- CentOS Linux release 7.7.1908 (Core)
- Linux 4.4.222-1.el7.elrepo.x86_64 #1 SMP Mon May 4 19:25:23 EDT 2020 x86_64 x86_64 x86_64 GNU/Linux

# 原因分析

怀疑是我操作系统内核过低导致的，见方法二中操作系统内核信息。怀疑是内核版本问题的原因是在我另外一台主机上不存在这个问题，正常主机的版本信息如下：

- CentOS Linux release 7.2.1511 (Core)
- Linux 5.4.107-1.el7.elrepo.x86_64 #1 SMP Sat Mar 20 09:47:18 EDT 2021 x86_64 x86_64 x86_64 GNU/Linux

```Shell
# yum -y update
# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# sudo yum -y install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
# yum --disablerepo="*" --enablerepo="elrepo-kernel" list available | grep kernel-ml
kernel-ml-doc.noarch                    5.14.9-2.el7.elrepo        elrepo-kernel
kernel-ml-headers.x86_64                5.14.9-2.el7.elrepo        elrepo-kernel
kernel-ml-tools.x86_64                  5.14.9-2.el7.elrepo        elrepo-kernel
kernel-ml-tools-libs.x86_64             5.14.9-2.el7.elrepo        elrepo-kernel
kernel-ml-tools-libs-devel.x86_64       5.14.9-2.el7.elrepo        elrepo-kernel
# yum --enablerepo=elrepo-kernel install kernel-ml
# yum -y --enablerepo=elrepo-kernel install kernel-ml-{devel,headers,perf}
已加载插件：fastestmirror, langpacks, priorities
Loading mirror speeds from cached hostfile
 * base: mirrors.nju.edu.cn
 * elrepo: mirrors.tuna.tsinghua.edu.cn
 * elrepo-kernel: mirrors.tuna.tsinghua.edu.cn
 * epel: mirrors.bfsu.edu.cn
 * extras: mirrors.163.com
 * updates: mirrors.163.com
软件包 kernel-ml-devel-5.14.9-2.el7.elrepo.x86_64 已安装并且是最新版本
没有可用软件包 kernel-ml-perf。
正在解决依赖关系
--> 正在检查事务
---> 软件包 kernel-ml-headers.x86_64.0.5.14.9-2.el7.elrepo 将被 安装
--> 处理 kernel-ml-headers-5.14.9-2.el7.elrepo.x86_64 与 kernel-headers < 5.14.9-2.el7.elrepo 的冲突
--> 解决依赖关系完成
错误：kernel-ml-headers conflicts with kernel-headers-3.10.0-1160.42.2.el7.x86_64
 您可以尝试添加 --skip-broken 选项来解决该问题
 您可以尝试执行：rpm -Va --nofiles --nodigest
# rpm -qa | grep kernel
kernel-devel-3.10.0-1160.42.2.el7.x86_64
kernel-headers-3.10.0-1160.42.2.el7.x86_64
kernel-devel-3.10.0-1062.el7.x86_64
kernel-ml-5.14.9-2.el7.elrepo.x86_64
abrt-addon-kerneloops-2.1.11-60.el7.centos.x86_64
kernel-ml-devel-5.14.9-2.el7.elrepo.x86_64
kernel-tools-libs-3.10.0-1160.42.2.el7.x86_64
kernel-3.10.0-1062.el7.x86_64
kernel-lt-4.4.222-1.el7.elrepo.x86_64
kernel-3.10.0-1160.42.2.el7.x86_64
kernel-tools-3.10.0-1160.42.2.el7.x86_64
# yum remove kernel-devel-3.10.0-1160.42.2.el7.x86_64 kernel-headers-3.10.0-1160.42.2.el7.x86_64 kernel-devel-3.10.0-1062.el7.x86_64 kernel-tools-libs-3.10.0-1160.42.2.el7.x86_64 kernel-3.10.0-1062.el7.x86_64 kernel-3.10.0-1160.42.2.el7.x86_64 kernel-tools-3.10.0-1160.42.2.el7.x86_64
# yum -y --enablerepo=elrepo-kernel install kernel-ml-{devel,headers,perf}
```

内核升级完成后，再次验证问题解决！！！