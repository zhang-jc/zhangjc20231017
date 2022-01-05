---
title: Ubuntu 20.04 Realtek 8852 无线网卡驱动
date: 2022-01-05 11:08:45
tags:
- Linux
- Ubuntu
categories:
- 操作系统
- Ubuntu
---

```Shell
sudo apt-get update
sudo apt-get install make gcc linux-headers-$(uname -r) build-essential git

git clone https://github.com/lwfinger/rtw89.git -b v5
cd rtw89 && make && sudo make install

sudo modprobe rtw89pci
```

执行完成后重启。

> 参考链接：[https://askubuntu.com/questions/1352731/ubuntu-20-04-no-wifi-adapter-found-lenovo-ideapad-realtek](https://askubuntu.com/questions/1352731/ubuntu-20-04-no-wifi-adapter-found-lenovo-ideapad-realtek)