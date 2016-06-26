title: Mac OS Shell 远程执行 Shell 命令
tags:
  - Mac OS
  - Shell
categories:
  - 操作系统
  - Mac OS
date: 2016-06-26 18:57:04
---

之前写过一个 Linux 下远程执行 Shell 脚本：

    #!/bin/bash

    SLAVES=(192.168.1.133 192.168.1.134)

    for slave in ${SLAVES[@]}
    do
      echo "==================$slave======================"
      ssh root@$slave << EOF
        ls -l /usr/
      EOF
    done

当然，所有的 slave 都配置过免密码登陆。

但是，这个脚本在 Mac OS 下至性的时候一直报错，信息如下：

    t.sh: line 12: syntax error: unexpected end of file

经过尝试发现在 Mac OS 下，远程执行命令前不能有空格，修改后脚本如下：

#!/bin/bash

SLAVES=(192.168.1.133 192.168.1.134)

for slave in ${SLAVES[@]}
do
  echo "==================$slave======================"
ssh root@$slave << EOF
  ls -l /usr/
EOF
done

再次执行成功。
