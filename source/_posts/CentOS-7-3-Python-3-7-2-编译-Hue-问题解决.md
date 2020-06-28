title: CentOS 7.3 Python 3.7.2 编译 Hue 问题解决
date: 2020-06-24 14:43:18
tags:
---

### 环境说明

- CentOS 7.3
- Python 3.7.2，我服务器上的安装是直接从其他服务器拷贝过来，然后配置环境变量的版本。正式因为这样我才遇到如下的问题。 

### 问题解决

#### python3.7-devel

    $ make apps
    "PYTHON_VER is python3.7."
    "Python 3 module install via pip"
    /opt/hue-4.7.1/Makefile.vars:65: *** "Error: must have python development packages for python3.7. Could not find Python.h. Please install python3.7-devel".  Stop.

尝试安装 python3.7-devel 包，发现该包不存在，所以通过以下方式解决：

    # ln -s /opt/python/include/python3.7m /usr/include/python3.7

我的 python 是安装在 /opt/ 目录下的。

#### 找不到对应版本的 python

    $ make apps
    "PYTHON_VER is python3.7."
    "Python 3 module install via pip"
    /opt/hue-4.7.1/Makefile.vars:73: *** "Error: Need python version 2.7 or >= 3.5".  Stop.

创建以下软连接解决：

    # ln -s /opt/python/bin/python3.7 /usr/bin/python3.7

#### 找不到 npm 命令

    npm --version
    /bin/bash: npm: command not found
    make[1]: *** [npm-install] Error 127

不能使用yum安装npm，因为安装的版本太低。需要安装 node 10 以上的版本：

    # cd /opt/
    # wget -c https://npm.taobao.org/mirrors/node/latest-v10.x/node-v10.21.0-linux-x64.tar.gz
    # tar xzf node-v10.21.0-linux-x64.tar.gz
    # ln -s node-v10.21.0-linux-x64 node

#### No module named 'pysqlite2'

    Traceback (most recent call last):
      File "/opt/hue-4.7.1/build/env/lib/python3.7/site-packages/django/db/backends/sqlite3/base.py", line 31, in <module>
        from pysqlite2 import dbapi2 as Database
    ModuleNotFoundError: No module named 'pysqlite2'


Python3 只支持 sqlite3：

    # pip install pysqlite
    Collecting pysqlite
      Using cached pysqlite-2.8.3.tar.gz (80 kB)
        ERROR: Command errored out with exit status 1:
         command: /opt/hue-4.7.1/build/env/bin/python3.7 -c 'import sys, setuptools, tokenize; sys.argv[0] = '"'"'/tmp/pip-install-42esl1q6/pysqlite/setup.py'"'"'; __file__='"'"'/tmp/pip-install-42esl1q6/pysqlite/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base /tmp/pip-pip-egg-info-v9a4at6e
             cwd: /tmp/pip-install-42esl1q6/pysqlite/
        Complete output (1 lines):
        pysqlite is not supported on Python 3. When using Python 3, use the sqlite3 module from the standard library.
        ----------------------------------------
    ERROR: Command errored out with exit status 1: python setup.py egg_info Check the logs for full command output.
    (env) [root@ycluster-resourcemanager1 bin]# pip install pysqlite3
    Collecting pysqlite3
      Downloading pysqlite3-0.4.3.tar.gz (40 kB)
         |████████████████████████████████| 40 kB 682 kB/s 
    Building wheels for collected packages: pysqlite3
      Building wheel for pysqlite3 (setup.py) ... done
      Created wheel for pysqlite3: filename=pysqlite3-0.4.3-cp37-cp37m-linux_x86_64.whl size=128283 sha256=9a55112d80511438c46512efb0f4021301ba7b04af2897b6d6746158d5cd0fe5
      Stored in directory: /root/.cache/pip/wheels/85/19/01/0a17a6cacac8cf8a4e8e8994d314abf10a9b11a1c4bc18218a
    Successfully built pysqlite3
    Installing collected packages: pysqlite3
    Successfully installed pysqlite3-0.4.3

重新编译，但是问题依旧：

    Traceback (most recent call last):
      File "/opt/hue-4.7.1/build/env/lib/python3.7/site-packages/django/db/backends/sqlite3/base.py", line 33, in <module>
        from sqlite3 import dbapi2 as Database
      File "/opt/python/lib/python3.7/sqlite3/__init__.py", line 23, in <module>
        from sqlite3.dbapi2 import *
      File "/opt/python/lib/python3.7/sqlite3/dbapi2.py", line 27, in <module>
        from _sqlite3 import *
    ModuleNotFoundError: No module named '_sqlite3'

解决方案是在系统中安装 sqlit 相关的包，然后重新编译安装 Python3：

    # yum install sqlite*
    
安装Pyhton3参考博文：http://zhang-jc.github.io/2018/11/19/Centos7-3-%E5%AE%89%E8%A3%85-Python3/

#### 重试可以解决的问题

编译的过程可能会出现以下错误，直接重试即可：

    ERROR: Could not find a version that satisfies the requirement threadloop<2,>=1 (from jaeger-client==4.3.0->-r /opt/hue-4.7.1/desktop/core/requirements.txt (line 30)) (from versions: none)
    ERROR: No matching distribution found for threadloop<2,>=1 (from jaeger-client==4.3.0->-r /opt/hue-4.7.1/desktop/core/requirements.txt (line 30))
    
    ERROR: Could not find a version that satisfies the requirement vine<5.0.0a1,>=1.1.3 (from amqp<2.7,>=2.6.0->kombu<5.0,>=4.2.0->celery==4.2.1->-r /opt/hue-4.7.1/desktop/core/requirements.txt (line 7)) (from versions: none)
    ERROR: No matching distribution found for vine<5.0.0a1,>=1.1.3 (from amqp<2.7,>=2.6.0->kombu<5.0,>=4.2.0->celery==4.2.1->-r /opt/hue-4.7.1/desktop/core/requirements.txt (line 7))

### 总结

    通过查看源代码可以轻松定位并解决以上问题。所以，快乐的享受有源代码的日子吧^_^
