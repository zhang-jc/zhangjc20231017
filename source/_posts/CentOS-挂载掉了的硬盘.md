title: CentOS 挂载掉了的硬盘
tags:
  - Linux
  - CentOS
categories:
  - 操作系统
  - Linux
date: 2017-10-17 14:03:19
---


如果服务器硬盘因为某种原因掉了（例如服务器搬迁后），此时使用 df 命令看不到需要的磁盘：

    $ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sda3       267G   14G  240G   6% /
    tmpfs            63G     0   63G   0% /dev/shm
    /dev/sda1       194M   28M  157M  15% /boot

<!-- more -->

使用 fdisk 命令查看磁盘情况：

    $ fdisk -l
    
    Disk /dev/sda: 299.4 GB, 299439751168 bytes
    255 heads, 63 sectors/track, 36404 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x000b6c80
    
       Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *           1          26      204800   83  Linux
    Partition 1 does not end on cylinder boundary.
    /dev/sda2              26        1070     8388608   82  Linux swap / Solaris
    /dev/sda3            1070       36405   283827200   83  Linux
    
    WARNING: GPT (GUID Partition Table) detected on '/dev/sdb'! The util fdisk doesn't support GPT. Use GNU Parted.
    
    
    Disk /dev/sdb: 66006.7 GB, 66006668017664 bytes
    255 heads, 63 sectors/track, 8024853 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disk identifier: 0x00000000
    
       Device Boot      Start         End      Blocks   Id  System
    /dev/sdb1               1      267350  2147483647+  ee  GPT
    Partition 1 does not start on physical sector boundary.

从上面的信息可以看出 /dev/sdb 没有挂载。因为是原来的磁盘掉了，而且文件系统目录也是已知的，所以只需要重新挂载即可。注意，千万不要重新格式化，否则原来磁盘上的数据都会丢失。挂载命令如下：

    $ mount /dev/sdb1 /data
    
再次查看：

    $ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sda3       267G   14G  240G   6% /
    tmpfs            63G     0   63G   0% /dev/shm
    /dev/sda1       194M   28M  157M  15% /boot
    /dev/sdb1        61T   23T   38T  38% /data

磁盘已经挂载成功。
