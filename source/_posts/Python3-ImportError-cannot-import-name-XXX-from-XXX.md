title: 'Python3 ImportError: cannot import name ''XXX'' from ''XXX'''
date: 2019-12-11 18:52:53
tags:
- Python3
categories:
- 语言
- Python3
---

例如如下错误：

    $ python3 git.py 
    Traceback (most recent call last):
      File "git.py", line 1, in <module>
        from git import Repo
      File "/home/a/git.py", line 1, in <module>
        from git import Repo
    ImportError: cannot import name 'Repo' from 'git' (/home/a/git.py)

这种错误基本都是因为 python 脚本的名称与模块的名称重复导致的。像我这个例子中就是因为 git.py 与 GitPython 模块中的文件名一样导致的。