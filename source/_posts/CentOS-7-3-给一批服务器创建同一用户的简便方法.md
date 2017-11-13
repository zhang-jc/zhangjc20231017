title: CentOS 7.3 给一批服务器创建同一用户的简便方法
tags:
  - CentOS
categories:
  - 操作系统
  - Linux
date: 2017-11-13 23:59:42
---

  
场景是：为一批服务器创建同一用户，用户名及密码相同。

<!-- more -->

第一步：打通某台服务器到其他服务器的 ssh 免密钥登陆。将需要的 IP 保存的文件 ips.txt 中。如下：

    172.16.72.58
    172.16.72.59
    172.16.72.60
    172.16.72.62
    172.16.72.64
    172.16.72.65

使用以下命令进行操作，需要输入密码。

    cat ips.txt | xargs -i ssh-copy-id root@{}
    
第二步：远程执行命令脚本 remote-shell.sh

    #!/bin/bash
    
    slaves=$1
    command=$2
      cat ${slaves} | while read ip
      do
        echo "--------${ip}--------"
    ssh -p65522 ${ip} << EOF
      eval "${command}"
      exit
    EOF
      done

第三步：创建用户 test

    sh remote-shell.sh ips.txt "useradd test"

第四步：修改用户密码

    sh remote-shell.sh ips.txt 'echo "test:afeafefa"|chpasswd'
