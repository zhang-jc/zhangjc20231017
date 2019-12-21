title: Python3 命令行交互不能使用方向键
date: 2019-12-21 17:25:50
tags:
- Python3
categorires:
- 语言
- Python3
---

自定义安装 Python3 后在命令行使用方向键时出现以下问题：

    $ python3
    Python 3.7.4 (default, Dec 11 2019, 17:40:08) 
    [GCC 7.4.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> ^[[A^[[B^[[C^[[D

解决方法是安装 readline：

    pip3 install readline
