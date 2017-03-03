title: 修改 Linux 符号链接的属主和组
tags:
  - Linux
categories:
  - 操作系统
  - Linux
date: 2017-03-03 11:11:48
---


使用以下命令修改 Linux 系统符号链接的数据和组：

    chown -h user /path
    chgrp -h user /path

<!-- more -->

当遇到不熟悉的命令，man 是一个好习惯：

    man chown
    -h, --no-dereference
              affect  each  symbolic  link  instead  of any referenced file (useful only on systems that can
              change the ownership of a symlink)