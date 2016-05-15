title: Linux下Nodejs应用service配置
tags:
  - 操作系统
  - Linux
  - service
  - Nodejs
categories:
  - 语言
  - Nodejs
date: 2016-05-16 00:12:51
---

Linux 的 service 命令用于对系统服务进行管理，比如启动（start）、停止（stop）、重启（restart）、查看状态（status）等。service 命令本身是一个 shell 脚本，它在 /etc/init.d/ 目录查找指定的服务脚本，然后调用该服务脚本来完成任务。

下面以基于 Nodejs 开发的名称为 data-inspector 的应用为例，说明 Linux service 的配置。

第一步：在 /frin/DataInspector 下创建指向 node 命令的软链接 data-inspector，执行 ll 命令查看该文件的信息如下：

    lrwxrwxrwx  1 root root     21 12月 30 15:42 data-inspector -> /usr/bin/node

> 这样做的目的是为自己的应用进程起一个不同的名字。

第二步：在 /etc/init.d/ 下创建文件 data-inspector，设置权限为 755，内容如下：

    #!/bin/sh
    #
    # data-inspector
    #
    # description: data-inspector
    # processname: data-inspector

    case "$1" in
      start)
        echo "Starting data-inspector"
        cd /frin/DataInspector
        rm -f data-inspector.log
        nohup ./data-inspector ./bin/www > data-inspector.log 2>&1 &
        sleep 1s
        echo "started data-inspector"
        ;;
      stop)
        PID=`pidof data-inspector`
        echo "Stopping data-inspector"
        if [ ! -z "$PID" ]; then
          kill -9 $PID
        fi
        echo "stoped data-inspector"
        ;;
      restart)
        $0 stop
        $0 start
      ;;
    *)
       echo "Usage: service data-inspector {start|stop|restart}"
    esac

    exit 0

> 注意，如果 node ／usr/bin 或 /usr/sbin 下，服务会因为找不到 node 命令失败。可以在 /usr/bin 下创建 node 的软链接。

完成之后执行以下命令可以对服务进行启动、停止、重启：

    $service data-inspector start
    $service data-inspector stop
    $service data-inspector restart
