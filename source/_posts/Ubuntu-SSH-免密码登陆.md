title: Ubuntu SSH 免密码登陆
tags:
  - Ubuntu
  - SSH
'-categories':
  - 操作系统
  - Ubuntu
date: 2016-06-26 07:44:08
---

使用 Ubuntu 系统远程登陆时，一般使用用户名加密码登陆，但这种方式每次需要输入用户名和密码，比较繁琐。我们可以用更具安全性的 RSA 密钥认证方式避免频繁输入用户名和密码。

<!-- more -->

### 应用场景

如果我们需要从一台机器上同时操作多台 Ubuntu 服务器，免密码登陆操作起来就方便多了。

### 实现步骤
#### 安装 SSH 服务

在 Ubuntu 服务器上安装 SSH 服务，方法参见另外一篇博客：[Ubuntu 安装 SSH，并开启 root 远程登录](http://zhang-jc.github.io/2016/05/25/Ubuntu-%E5%AE%89%E8%A3%85-SSH%EF%BC%8C%E5%B9%B6%E5%BC%80%E5%90%AF-root-%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95/)。

#### Client 端生成公钥和密钥

使用以下命令生成客户端公钥和密钥：

    $ ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

生成的密钥对在目录 ~/.ssh/ 下，id_rsa.pub 是公钥，id_rsa 是密钥。

#### 将 Client 端的公钥添加到 Server 端的公钥文件中

首先检查 Server 端需要认证的用户（如 root）的 Home 目录下，隐藏目录 .ssh 中是否存在一个名为 authorized_keys 的文件，若不存在，使用命令：touch authorized_keys 创建一个空文件创建完成后，则可以执行如下步骤：

1. 复制 Clinet 端公钥的内容
2. 将复制的 Client 端公钥内容粘贴至 Server 端的 authorized_keys 文件中，保存文件。
3. 更改 authorized_keys 文件的权限为 600，执行命令 chmod 600  authorized_keys
4. 重启服务端 sshd 服务：service sshd restart

#### 验证无密码登陆

在客户端输入命令：ssh root@192.168.1.133（用户名和IP自行替换），看到如下结果则无密码登陆配置成功。
![SSH 免密码登陆](/uploads/20160626/ssh.png)

> 在将客户端公钥添加到服务端时，如果是从一台客户端登陆多台服务器，则可以先将公钥添加至客户端的 authorized_keys 文件，直接将客户端 authorized_keys 文件 scp 到服务器对应的目录下，然后重启服务器 sshd 服务。但这个过程中还是需要先输入用户名和密码登陆服务器重启 sshd 服务。是否有更便捷的方式有待学习。
