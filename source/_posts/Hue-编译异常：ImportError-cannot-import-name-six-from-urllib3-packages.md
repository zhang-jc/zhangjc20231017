title: 'Hue 编译异常：ImportError: cannot import name ''six'' from ''urllib3.packages'''
date: 2020-06-23 17:14:36
tags:
- Hue
- Python3
categories:
- 大数据
- Hue
---

在编译Hue的时候出现错误信息如下：

    Running '/home/zhangjc/ysten/git/ysten-hue/build/env/bin/hue makemigrations --noinput' with None
    Traceback (most recent call last):
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/bin/hue", line 33, in <module>
        sys.exit(load_entry_point('desktop', 'console_scripts', 'hue')())
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/manage_entry.py", line 225, in entry
        execute_from_command_line(sys.argv)
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/lib/python3.7/site-packages/django/core/management/__init__.py", line 364, in execute_from_command_line
        utility.execute()
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/lib/python3.7/site-packages/django/core/management/__init__.py", line 338, in execute
        django.setup()
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/lib/python3.7/site-packages/django/__init__.py", line 27, in setup
        apps.populate(settings.INSTALLED_APPS)
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/lib/python3.7/site-packages/django/apps/registry.py", line 108, in populate
        app_config.import_models()
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/lib/python3.7/site-packages/django/apps/config.py", line 202, in import_models
        self.models_module = import_module(models_module_name)
      File "/home/zhangjc/ysten/git/ysten-hue/build/env/lib/python3.7/importlib/__init__.py", line 127, in import_module
        return _bootstrap._gcd_import(name[level:], package, level)
      File "<frozen importlib._bootstrap>", line 1006, in _gcd_import
      File "<frozen importlib._bootstrap>", line 983, in _find_and_load
      File "<frozen importlib._bootstrap>", line 967, in _find_and_load_unlocked
      File "<frozen importlib._bootstrap>", line 677, in _load_unlocked
      File "<frozen importlib._bootstrap_external>", line 728, in exec_module
      File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/models.py", line 47, in <module>
        from useradmin.models import User, Group, get_organization
      File "/home/zhangjc/ysten/git/ysten-hue/apps/useradmin/src/useradmin/models.py", line 56, in <module>
        from desktop.lib.connectors.models import _get_installed_connectors, Connector
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/connectors/models.py", line 29, in <module>
        from desktop.lib.connectors.types import get_connectors_types
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/connectors/types.py", line 24, in <module>
        from desktop.lib.exceptions_renderable import PopupException
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/exceptions_renderable.py", line 31, in <module>
        import desktop.lib.django_util
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/django_util.py", line 42, in <module>
        import desktop.lib.thrift_util
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/thrift_util.py", line 49, in <module>
        from desktop.lib.thrift_.http_client import THttpClient
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/thrift_/http_client.py", line 26, in <module>
        from desktop.lib.rest.http_client import HttpClient
      File "/home/zhangjc/ysten/git/ysten-hue/desktop/core/src/desktop/lib/rest/http_client.py", line 32, in <module>
        from urllib3.contrib import pyopenssl
      File "/usr/lib/python3/dist-packages/urllib3/contrib/pyopenssl.py", line 62, in <module>
        from ..packages import six
    ImportError: cannot import name 'six' from 'urllib3.packages' (/usr/lib/python3/dist-packages/urllib3/packages/__init__.py)

错误原因：根据错误信息可以看到是“/usr/lib/python3/dist-packages/urllib3”这个包的问题。正确的应该是引用“build/env/lib/python3.7/site-packages/”下的包。查看"/usr/lib/python3/dist-packages/urllib3"下的包信息，发现包版本比较低。

    $ ls -l /usr/lib/python3/dist-packages/
    drwxr-xr-x  6 root root   4096 1月  13 17:07 urllib3
    drwxr-xr-x  2 root root   4096 1月  13 17:06 urllib3-1.22.egg-info

解决方法：删除有问题的包后再试成功。

    $ sudo rm -rf /usr/lib/python3/dist-packages/urllib3*

查看“build/env/lib/python3.7/site-packages/”下的urllib3包信息：

    $ ls -l build/env/lib/python3.7/site-packages/|grep urllib3
    drwxr-xr-x  6 zhangjc zhangjc   4096 6月  23 17:06 urllib3
    drwxr-xr-x  2 zhangjc zhangjc   4096 6月  23 17:06 urllib3-1.25.9.dist-info