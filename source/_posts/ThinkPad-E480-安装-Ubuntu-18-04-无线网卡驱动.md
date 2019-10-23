title: ThinkPad E480 安装 Ubuntu 18.04 无线网卡驱动
date: 2019-10-23 12:02:00
tags:
- Ubuntu
- Linux
categories:
- 操作系统
- Ubuntu
---
### 基本信息

- Ubuntu 18.04
- ThinkPad E480
- 使用下面的命令查看 Linux 内核：

    $ uname -r
    5.0.0-32-generic

- 使用以下命令查看网卡型号信息：

    $ sudo lshw -C network
      *-network UNCLAIMED
           description: Network controller
           product: RTL8821CE 802.11ac PCIe Wireless Network Adapter
           vendor: Realtek Semiconductor Co., Ltd.
           physical id: 0
           bus info: pci@0000:05:00.0
           version: 00
           width: 64 bits
           clock: 33MHz
           capabilities: pm msi pciexpress cap_list
           configuration: latency=0
           resources: ioport:b000(size=256) memory:f2100000-f210ffff

让人头疼的就是这款网卡 RTL8821CE！！这款网卡再 Linux 内核还未支持，只能自行编译安装。

### 参考网址

- https://blog.csdn.net/fljhm/article/details/79281655
- https://unix.stackexchange.com/questions/379049/realtek-rtl8821ce-wifi-driver-problem-in-linux-mint-18-2

### 驱动源码下载

以下源代码是我修改过之后，可以成功编译。原始的代码编译过程中会有不少异常，可以参考上面的网址解决一部分，对于头文件找不到的按照提示将缺少的头文件拷贝到对应的位置即可。

源码下载：[rtl8821ce.zip](/uploads/20191023/rtl8821ce.zip)