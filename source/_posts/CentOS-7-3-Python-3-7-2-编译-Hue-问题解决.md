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


### 总结

    通过查看源代码可以轻松定位并解决以上问题。所以，快乐的享受有源代码的日子吧^_^