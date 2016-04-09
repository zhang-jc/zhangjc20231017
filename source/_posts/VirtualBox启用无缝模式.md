title: VirtualBox启用无缝模式
date: 2015-11-22 10:05:33
tags:
- VirtualBox
- Ubuntu
categories:
- 操作系统
- 虚拟机
---
VirtualBox是个不错的产品，免费而且轻量。平时学习、开发使用很方便。vbox安装ubuntu后不能启用无缝模式，外观不能全屏，操作也很不方便，如下图：

![vbox非无缝模式](/uploads/20151122/vbox1.png)

要启用vbox无缝模式需要安装增强功能，如下图：

![vbox增强功能](/uploads/20151122/vbox2.png)

点击“安装增强功能”后，在客户机中会出现安装界面，根据提示安装即可。如下图：

![vbox增强功能安装](/uploads/20151122/vbox3.png)

如果提示没有光驱错误（如下图一），则需要在虚拟机设置中增加光驱，并且将增强功能的包添加进该光驱（如下图二）。增强功能包通常在vbox安装目录下，名为 **VBoxGuestAdditions.iso** 的文件。

<center>图一：安装增强功能错误</center>
![安装增强功能错误](/uploads/20151122/vbox4.png)

<center>图二：虚拟机配置光驱</center>
![虚拟机配置光驱](/uploads/20151122/vbox5.png)

注意，配置光驱时需要先将虚拟机关机，配置完成后再开启。
