---
title: sudoers文件修改格式错误恢复
date: 2021-12-27 19:07:12
tags:
- Linux
- Ubuntu
categories:
- 操作系统
- Linux
---

异常信息如下：

```Log
$ sudo -i
>>> /etc/sudoers.d/dev: 语法错误 near line 1 <<<
sudo: /etc/sudoers.d/dev 中第 1 行附近有解析错误
sudo: 没有找到有效的 sudoers 资源，退出
sudo: 无法初始化策略插件
```

使用以下命令编辑sudoers文件：

```Shell
pkexec visudo
```