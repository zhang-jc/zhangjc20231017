---
title: Ansible远程调起后台进程
date: 2021-10-26 00:45:01
tags:
- Ansible
---

以下命令调起NodeManager后紧跟着就会退出：

```Shell
ansible slaves -m shell -a "yarn --daemon start nodemanager"
```

应该如下：

```Shell
ansible slaves -m shell -a "nohup yarn --daemon start nodemanager"
```