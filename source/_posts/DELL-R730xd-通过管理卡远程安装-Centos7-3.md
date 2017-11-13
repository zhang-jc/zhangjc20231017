title: DELL R730xd 通过管理卡远程安装 CentOS 7.3
tags:
  - Linux
  - CentOS
categories:
  - 操作系统
  - Linux
date: 2017-11-13 23:16:30
---

### 环境说明

- 服务器：DELL R730xd
- 远程管理卡：iDRAC8
- 安装系统：CentOS 7.3
- 客户端操作系统：Ubuntu 16.04
- JDK 8

### 安装步骤

#### 下载CentOS

下载地址：<https://www.centos.org/download/>

默认只会链接到最新版本。如：<http://mirrors.sohu.com/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1708.iso>。查看所有版本可以到上层目录：<http://mirrors.sohu.com/centos/>。

#### 登陆虚拟控制台

登录远程管理 https://+ip。选择“虚拟控制台”，然后点击“启动虚拟控制台”浏览器会提示下载一个“viewer.jnlp”文件。下载该文件并保存。![虚拟控制台](/uploads/20171019/virtual_console.png)

通过命令行执行以下命令启动虚拟控制台：

    $ javaws viewer.jnlp

![JDK 升级提示](/uploads/20171019/java_update_tip.png)
JDK 升级提示，选择 “Later”。

![安全提示](/uploads/20171019/security_warning.png)
安全警告提示选择“继续”。

![是否运行](/uploads/20171019/launch_ask.png)
选择“运行”。

![安全提示2](/uploads/20171019/security_warning2.png)
选择“运行”。
![虚拟控制台](/uploads/20171019/virtual_console_pic.png)

### 系统安装

连接“虚拟介质”，然后“映射CD/DVD”，选中已下载的 iso 文件。然后点击“映射设备”。
![连接虚拟介质](/uploads/20171019/cd_dvd.png)

![映射设备](/uploads/20171019/virtual_medium.png)

重新启动服务器，按 F11 进入 BIOS 设置，改为从虚拟介质启动。
