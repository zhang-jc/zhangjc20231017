---
title: >-
  OSError: libmediainfo.so.0: cannot open shared object file: No such file or
  directory
date: 2023-01-28 14:16:29
tags:
- Linux
- Ubuntu
- 操作系统
---

Ubuntu 20.04安装libmediainfo-dev问题解决：

```Shell
sudo apt-get update -y
sudo apt-get install -y libmediainfo-dev
```