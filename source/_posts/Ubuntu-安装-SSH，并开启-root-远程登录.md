title: Ubuntu 安装 SSH，并开启 root 远程登录
tags:
  - Ubuntu
categories:
  - 操作系统
  - Ubuntu
date: 2016-05-25 05:18:28
---

#### 安装 SSH

    apt-get install ssh

其中包含了 openssh-client 和 openssh-server。

<!-- more -->

#### 开启 root 远程登录

修改配置文件 /etc/ssh/sshd_config 中的 PermitRootLogin 参数值为 yes：  
![PermitRootLogin](/uploads/20160525/ssh-PermitRootLogin.png)

重启 SSH 服务：

    service ssh restart

#### PermitRootLogin 参数

指定 root 是否可以用 [ssh](http://man.openbsd.org/ssh.1) 登录。参数值必须是“yes”、“prohibit-password”、“without-password”、“orced-commands-only”或者“no”。默认值是“prohibit-password”。

如果这个选项设置为“prohibit-password”、“without-password”，通过密码和键盘交互的授权方式对 root 用户禁用。

如果这个选项设置为“orced-commands-only”，root 用户只允许通过公钥授权登录，但只是指定了 *conmmand* 选项的时候（这可能对远程备份是有好处的，即使 root 不允许登录）。其他授权方式对 root 无效。

如果这个选项设置为“no”，root 不允许登录。

其他参数说明，请查阅[官方文档](http://man.openbsd.org/sshd_config)。
