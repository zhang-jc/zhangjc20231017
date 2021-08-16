---
title: Linux下基于root指定用户执行命令的方法
date: 2021-08-16 17:33:03
tags:
- Linux
- Shell
categories:
- 操作系统
- Linux
---

Docker在构建镜像阶段无法配置免密码sudo。但是在实际需求场景中会遇到需要使用sudo的场景。所以，我的解决思路是镜像构建及CMD使用root，在CMD的脚本中执行需要sudo的部分，然后使用普通用户启动服务进程。

当然，基于root使用普通用户启动进程可以选择su或者runuser。我使用的是su：

```Shell
#!/bin/bash

cd `dirname $0`

${RANGER_HOME}/enable-hive-plugin.sh
if [ $? -ne 0 ];then
    echo "启用ranger plugin错误！"
    exit 1
fi

su -mp -c '/opt/hive/bin/hiveserver2' hive
```

注意：-mp 是保留root设置的环境变量。如果不需要保留这些变量则可以使用-l选项。使用man命令查看su完整的手册。