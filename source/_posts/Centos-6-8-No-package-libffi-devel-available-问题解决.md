title: Centos 6.8 No package libffi-devel available 问题解决
date: 2018-11-27 19:08:16
tags:
- Linux
- CentOS
categories:
- 操作系统
- Linux
---

在 CentOS 6.8 安装 libffi-devel 的时候出现以下问题：

    #yum install -y libffi-devel
    Loaded plugins: product-id, refresh-packagekit, search-disabled-repos, security, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    Setting up Install Process
    cdrom                                                                                                                                         | 4.1 kB     00:00 ...
    No package libffi-devel available.
    Error: Nothing to do

可以通过以下方式解决。

第一步，从以下地址找到对应的 libffi-bedel 版本：https://rpmfind.net/linux/rpm2html/search.php?query=libffi-devel

第二步，通过以下命令安装：

    #yum install https://rpmfind.net/linux/centos/6.10/os/x86_64/Packages/libffi-devel-3.0.5-3.2.el6.x86_64.rpm
