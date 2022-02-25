---
title: Linux命令行命令自动补全
date: 2022-02-24 14:59:43
tags:
- Linux
categories:
- 操作系统
- Linux
---

GitHub项目：[https://github.com/scop/bash-completion](https://github.com/scop/bash-completion)。

确认bash版本：

```Shell
bash --version
```

make check过程出现错误信息：

```Text
/bin/sh: pytest: command not found
make[4]: *** [check-local] Error 127
```

由于我的环境没有安装pip，所以先安装。安装参考[https://pip.pypa.io/en/stable/installation/](https://pip.pypa.io/en/stable/installation/)。采用get-pip.py方式安装，该文件下载信息如下：

- Python3.7及以上版本：<https://bootstrap.pypa.io/get-pip.py>
- Python3.7以下版本：<https://bootstrap.pypa.io/pip/2.7/get-pip.py>

安装执行命令：

```Shell
python get-pip.py
```

然后安装pytest：

```Shell
pip install pytest
```

再次执行make check报错信息如下：

```Text
ABS_TOP_BUILDDIR="/root/bash-completion" \
	pytest  .
ImportError while loading conftest '/root/bash-completion/test/t/conftest.py'.
  File "/root/bash-completion/test/t/conftest.py", line 32
    items: Iterable[str],
         ^
SyntaxError: invalid syntax
make[4]: *** [check-local] Error 4
```

怀疑是Python版本导致的问题，由于时间关系没继续研究。反正该错误不影响安装和正常使用。