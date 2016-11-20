title: Shell $0
tags:
  - Shell
categories:
  - 语言
  - Shell
date: 2016-09-11 03:33:03
---


我们已经知道在 Shell 中 $0 表示 Shell 脚本的文件名，但在有脚本调用的情形中，子脚本中的 $0 会是什么值呢？我们通过下面的实例来看。

<!-- more -->

已测试系统列表：

- Mac OS X EI Capitan 10.11.6
- Ubuntu 16.04 LTS (GNU/Linux 4.4.0-28-generic x86_64)

父脚本 a.sh，位置 test1/a.sh，内容如下：

    !/usr/bin/env bash

    echo "$0"
    . ../test2/b.sh

子脚本 b.sh，位置 test2/b.sh，内容如下：

    #!/usr/bin/env bash

    echo "the sub script is: $0"

此时执行父脚本输出结果是：

    $ sh a.sh
    the main script is a.sh
    the sub script is: a.sh

如果父脚本内容如下：

    #!/usr/bin/env bash

    echo "the main script is $0"
    ../test2/b.sh

则输出结果为：

    $ sh a.sh 
    the main script is a.sh
    the sub script is: ../test2/b.sh

可见，在父脚本中调用子脚本的不同，在子脚本中 $0 的值也不同。至于为什么会这样本人需要继续学习以找到答案。

> 测试过程中注意给脚本赋可执行权限。