---
title: Window 10 配置 PIP 代理
date: 2023-01-17 16:54:35
tags:
- Python3
- Window
- 操作系统
---

简单的方式就是直接用命令，例如设置阿里代理：

```Shell
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
pip config set install.trusted-host mirrors.aliyun.com
```

配置信息会保存在下面路径的配置文件中，如果该文件不存在则会自动创建。另外一种方式是直接修改该配置文件内容。

> C:\Users\{user}\AppData\Roaming\pip\pip.ini

配置完成后，上面配置文件的内容如下：

```INI
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host = mirrors.aliyun.com
```