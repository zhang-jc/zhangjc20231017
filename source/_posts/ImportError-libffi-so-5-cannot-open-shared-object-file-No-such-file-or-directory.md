title: >-
  ImportError: libffi.so.5: cannot open shared object file: No such file or
  directory
date: 2019-10-10 13:41:47
tags:
- Python3
- Requests
- CentOS
- Linux
categories:
- 工具箱
- Requests
---
在 CentOS 7.4 中通过源码的方式安装 Requests 时，报错信息如下：

    # pip3 install .
    Processing /data/setup/psf-requests-fab1fd1
        ERROR: Command errored out with exit status 1:
         command: /opt/Python-3.7.2/bin/python3.7 -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-req-build-nuwyypgj/setup.py'"'"'; __file__='"'"'/tmp/pip-req-build-nuwyypgj/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base pip-egg-info
             cwd: /tmp/pip-req-build-nuwyypgj/
        Complete output (11 lines):
        Traceback (most recent call last):
          File "<string>", line 1, in <module>
          File "/opt/Python-3.7.2/lib/python3.7/site-packages/setuptools/__init__.py", line 18, in <module>
            from setuptools.dist import Distribution, Feature
          File "/opt/Python-3.7.2/lib/python3.7/site-packages/setuptools/dist.py", line 31, in <module>
            from setuptools import windows_support
          File "/opt/Python-3.7.2/lib/python3.7/site-packages/setuptools/windows_support.py", line 2, in <module>
            import ctypes
          File "/opt/Python-3.7.2/lib/python3.7/ctypes/__init__.py", line 7, in <module>
            from _ctypes import Union, Structure, Array
        ImportError: libffi.so.5: cannot open shared object file: No such file or directory
        ----------------------------------------
    ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.

其实在 CentOS 7.4 下已经存在 libffi.so.6，解决方法是创建软连接，如下：

    # cd /usr/lib64
    # ln -s libffi.so.6 libffi.so.5
    # ln -s libffi.so.6.0.1 libffi.so.5.0.1