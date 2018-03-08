title: CentOS 7 查看磁盘文件系统格式
tags:
  - Linux
  - CentOS
categories:
  - 操作系统
  - Linux
date: 2018-03-08 15:19:35
---


前提是磁盘已经格式化并挂载，可以直接查看 fstab 文件：

    # cat /etc/fstab 

    #
    # /etc/fstab
    # Created by anaconda on Fri May  5 20:02:53 2017
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    /dev/mapper/centos-root /                       xfs     defaults        0 0
    UUID=12921ad4-88da-469d-94df-1aa2eaec6db4 /boot                   xfs     defaults        0 0
    /dev/mapper/centos-swap swap                    swap    defaults        0 0
    /dev/sda1 /data0                       xfs     defaults        0 0
    /dev/sdb1 /data1                       xfs     defaults        0 0
    /dev/sdc1 /data2                       xfs     defaults        0 0
    /dev/sdd1 /data3                       xfs     defaults        0 0
    /dev/sde1 /data4                       xfs     defaults        0 0
    /dev/sdf1 /data5                       xfs     defaults        0 0
    /dev/sdg1 /data6                       xfs     defaults        0 0
    /dev/sdh1 /data7                       xfs     defaults        0 0
    /dev/sdi1 /data8                       xfs     defaults        0 0
    /dev/sdj1 /data9                       xfs     defaults        0 0
    /dev/sdl1 /dataa                       xfs     defaults        0 0
    /dev/sdm1 /datab                       xfs     defaults        0 0

