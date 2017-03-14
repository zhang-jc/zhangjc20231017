title: Linux 远程 kill 进程及 $ 处理
tags:
  - Linux
categories:
  - 操作系统
  - Linux
date: 2017-03-14 14:40:03
---


在远程执行的命令中如果包含 $ 时需要转义，如下：

    ssh root@remote_host "ps -ef|grep process_name | grep -v 'grep ' | awk '{print $2}'"

以上语句的本意是得到进程的 ID，但实际输出会是进程的信息。应该使用如下形式：

    ssh root@remote_host "ps -ef|grep process_name | grep -v 'grep ' | awk '{print \$2}'"

<!-- more -->

>如果远程执行命令是在脚本文件中，则需要多重 转义。如：  
>sh exec.sh "ps -ef|grep process_name | grep -v 'grep ' | awk '{print \\\\\$2}'"  
>exec.sh 内容如下：  
>ssh root@remote_host << EOF  
>  eval "${command}"  
>  exit  
>EOF

使用以下命令可以远程 kill 进程：

  ssh root@remote_host "ps -ef|grep process_name | grep -v 'grep ' | awk '{print \$2}' | xargs kill -9"