title: Python3 创建虚拟环境
date: 2019-01-13 20:09:35
tags:
- Python3
categories:
- 语言
- Python3
---
Python 虚拟环境主要是为了解决 Python 多版本及模块间版本兼容的问题。创建虚拟环境的方法很简单，使用下面的命令即可：

    python3 -m venv PySparkEnv

PySparkEnv 即虚拟环境的主目录。如果要启用这个虚拟环境的话，使用以下命令：

    source PySparkEnv/bin/activate
