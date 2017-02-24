title: Hadoop 进程关闭时报 no 进程名 to stop
tags:
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-02-24 13:49:45
---

### 问题描述

在停止 Hadoop 的 resourcemanager 时遇到 no resourcemanager to stop 的问题。查看 resourcemanager 进程确实在运行。![no resourcemanager to stop](/uploads/20170224/no-resourcemanager-to-stop.png)

<!-- more -->

查看 yarn-daemon.sh 中 stop 部分的代码：

    (stop)

      if [ -f $pid ]; then
        TARGET_PID=`cat $pid`
        if kill -0 $TARGET_PID > /dev/null 2>&1; then
          echo stopping $command
          kill $TARGET_PID
          sleep $YARN_STOP_TIMEOUT
          if kill -0 $TARGET_PID > /dev/null 2>&1; then
            echo "$command did not stop gracefully after $YARN_STOP_TIMEOUT seconds: killing with kill -9"
            kill -9 $TARGET_PID
          fi
        else
          echo no $command to stop
        fi
        rm -f $pid
      else
        echo no $command to stop
      fi
      ;;

在停止 resourcemanager 的时候会先从 pid 文件中获取进程的 pid（TARGET_PID=`cat $pid`）。之后的操作都是对这个 pid 进行。默认情况下，这个 pid 的文件会放在 /tmp/ 目录下。到该目录下查看，确实没有进程的 pid 文件。

### 为什么 pid 文件不见了？

是因为系统安装了 tmpwatch。从 /etc/cron.daily/ 下可以看到 tmpwatch，内容如下：

    #! /bin/sh
    flags=-umc
    /usr/sbin/tmpwatch "$flags" -x /tmp/.X11-unix -x /tmp/.XIM-unix \
            -x /tmp/.font-unix -x /tmp/.ICE-unix -x /tmp/.Test-unix \
            -X '/tmp/hsperfdata_*' 10d /tmp
    /usr/sbin/tmpwatch "$flags" 30d /var/tmp
    for d in /var/{cache/man,catman}/{cat?,X11R6/cat?,local/cat?}; do
        if [ -d "$d" ]; then
            /usr/sbin/tmpwatch "$flags" -f 30d "$d"
        fi
    done

可以看到，/tmp 下超过 10 天的文件会被清理掉。

### 如何停止进程

从 yarn-daemon.sh 中可以看到，只要有 pid，执行 kill pid 结束进程。kill 是一种安全结束进程的方式，如果 kill 不起作用可以用 kill -9 强制结束进程。

### 设置 pid 文件位置

在 yarn-daemon.sh 中设置 pid 文件目录位置，如下：

    export YARN_PID_DIR=$HADOOP_YARN_HOME/pids
    if [ "$YARN_PID_DIR" = "" ]; then
      YARN_PID_DIR=/tmp
    fi
