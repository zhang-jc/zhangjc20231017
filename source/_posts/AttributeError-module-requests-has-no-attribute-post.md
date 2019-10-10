title: 'AttributeError: module ''requests'' has no attribute ''post'''
date: 2019-10-10 14:21:45
tags:
- Requests
- Python3
categories:
- 工具箱
- Requests
---
发现是因为安装的 Requests 模块权限的问题。安装完成后模型权限如下：

    # ls -l ./lib/python3.7/site-packages/requests
    total 212
    -rw-r----- 1 root root 19537 Sep 30 17:52 adapters.py
    -rw-r----- 1 root root  6496 Sep 30 17:52 api.py
    -rw-r----- 1 root root 10207 Sep 30 17:52 auth.py
    -rw-r----- 1 root root   453 Sep 30 17:52 certs.py
    -rw-r----- 1 root root  1782 Sep 30 17:52 compat.py
    -rw-r----- 1 root root 18430 Sep 30 17:52 cookies.py
    -rw-r----- 1 root root  3185 Sep 30 17:52 exceptions.py
    -rw-r----- 1 root root  3515 Sep 30 17:52 help.py
    -rw-r----- 1 root root   757 Sep 30 17:52 hooks.py
    -rw-r----- 1 root root  3922 Sep 30 17:52 __init__.py
    -rw-r----- 1 root root  1096 Sep 30 17:52 _internal_utils.py
    -rw-r----- 1 root root 34830 Sep 30 17:52 models.py
    -rw-r----- 1 root root   542 Sep 30 17:52 packages.py
    drwxr-x--- 2 root root  4096 Oct 10 13:22 __pycache__
    -rw-r----- 1 root root 29277 Sep 30 17:52 sessions.py
    -rw-r----- 1 root root  4188 Sep 30 17:52 status_codes.py
    -rw-r----- 1 root root  3005 Sep 30 17:52 structures.py
    -rw-r----- 1 root root 30176 Sep 30 17:52 utils.py
    -rw-r----- 1 root root   436 Sep 30 17:52 __version__.py

修改权限为 755 后问题解决。如下：

    # chmod -R 755 ./lib/python3.7/site-packages/requests
    # ls -l ./lib/python3.7/site-packages/requests
    total 212
    -rwxr-xr-x 1 root root 19537 Sep 30 17:52 adapters.py
    -rwxr-xr-x 1 root root  6496 Sep 30 17:52 api.py
    -rwxr-xr-x 1 root root 10207 Sep 30 17:52 auth.py
    -rwxr-xr-x 1 root root   453 Sep 30 17:52 certs.py
    -rwxr-xr-x 1 root root  1782 Sep 30 17:52 compat.py
    -rwxr-xr-x 1 root root 18430 Sep 30 17:52 cookies.py
    -rwxr-xr-x 1 root root  3185 Sep 30 17:52 exceptions.py
    -rwxr-xr-x 1 root root  3515 Sep 30 17:52 help.py
    -rwxr-xr-x 1 root root   757 Sep 30 17:52 hooks.py
    -rwxr-xr-x 1 root root  3922 Sep 30 17:52 __init__.py
    -rwxr-xr-x 1 root root  1096 Sep 30 17:52 _internal_utils.py
    -rwxr-xr-x 1 root root 34830 Sep 30 17:52 models.py
    -rwxr-xr-x 1 root root   542 Sep 30 17:52 packages.py
    drwxr-xr-x 2 root root  4096 Oct 10 13:22 __pycache__
    -rwxr-xr-x 1 root root 29277 Sep 30 17:52 sessions.py
    -rwxr-xr-x 1 root root  4188 Sep 30 17:52 status_codes.py
    -rwxr-xr-x 1 root root  3005 Sep 30 17:52 structures.py
    -rwxr-xr-x 1 root root 30176 Sep 30 17:52 utils.py
    -rwxr-xr-x 1 root root   436 Sep 30 17:52 __version__.py