title: Hue安装ImportError错误一例
date: 2021-01-13 11:50:09
tags:
- Hue
- Linux
categories:
- 大数据
- Hue
---

错误信息如下：

	Traceback (most recent call last):
	  File "/opt/hue-4.8.0/hue/build/env/bin/hue", line 11, in <module>
	    load_entry_point('desktop', 'console_scripts', 'hue')()
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/manage_entry.py", line 225, in entry
	    execute_from_command_line(sys.argv)
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/core/management/__init__.py", line 364, in execute_from_command_line
	    utility.execute()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/core/management/__init__.py", line 338, in execute
	    django.setup()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/__init__.py", line 27, in setup
	    apps.populate(settings.INSTALLED_APPS)
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/apps/registry.py", line 108, in populate
	    app_config.import_models()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/apps/config.py", line 202, in import_models
	    self.models_module = import_module(models_module_name)
	  File "/usr/lib64/python2.7/importlib/__init__.py", line 37, in import_module
	    __import__(name)
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/models.py", line 47, in <module>
	    from useradmin.models import User, Group, get_organization
	  File "/opt/hue-4.8.0/hue/apps/useradmin/src/useradmin/models.py", line 56, in <module>
	    from desktop.lib.connectors.models import _get_installed_connectors, Connector
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/connectors/models.py", line 29, in <module>
	    from desktop.lib.connectors.types import get_connectors_types
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/connectors/types.py", line 24, in <module>
	    from desktop.lib.exceptions_renderable import PopupException
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/exceptions_renderable.py", line 31, in <module>
	    import desktop.lib.django_util
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/django_util.py", line 42, in <module>
	    import desktop.lib.thrift_util
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/thrift_util.py", line 49, in <module>
	    from desktop.lib.thrift_.http_client import THttpClient
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/thrift_/http_client.py", line 26, in <module>
	    from desktop.lib.rest.http_client import HttpClient
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/rest/http_client.py", line 32, in <module>
	    from urllib3.contrib import pyopenssl
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/urllib3-1.25.8-py2.7.egg/urllib3/contrib/pyopenssl.py", line 46, in <module>
	    import OpenSSL.SSL
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/__init__.py", line 8, in <module>
	    from OpenSSL import crypto, SSL
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/crypto.py", line 16, in <module>
	    from OpenSSL._util import (
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/_util.py", line 6, in <module>
	    from cryptography.hazmat.bindings.openssl.binding import Binding
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/cryptography-2.9-py2.7-linux-x86_64.egg/cryptography/hazmat/bindings/openssl/binding.py", line 16, in <module>
	    from cryptography.hazmat.bindings._openssl import ffi, lib
	ImportError: /opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/cryptography-2.9-py2.7-linux-x86_64.egg/cryptography/hazmat/bindings/_openssl.so: undefined symbol: RSA_set0_factors
	Running '/opt/hue-4.8.0/hue/build/env/bin/hue migrate --fake-initial' with None
	Traceback (most recent call last):
	  File "/opt/hue-4.8.0/hue/build/env/bin/hue", line 11, in <module>
	    load_entry_point('desktop', 'console_scripts', 'hue')()
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/manage_entry.py", line 225, in entry
	    execute_from_command_line(sys.argv)
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/core/management/__init__.py", line 364, in execute_from_command_line
	    utility.execute()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/core/management/__init__.py", line 338, in execute
	    django.setup()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/__init__.py", line 27, in setup
	    apps.populate(settings.INSTALLED_APPS)
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/apps/registry.py", line 108, in populate
	    app_config.import_models()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/apps/config.py", line 202, in import_models
	    self.models_module = import_module(models_module_name)
	  File "/usr/lib64/python2.7/importlib/__init__.py", line 37, in import_module
	    __import__(name)
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/models.py", line 47, in <module>
	    from useradmin.models import User, Group, get_organization
	  File "/opt/hue-4.8.0/hue/apps/useradmin/src/useradmin/models.py", line 56, in <module>
	    from desktop.lib.connectors.models import _get_installed_connectors, Connector
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/connectors/models.py", line 29, in <module>
	    from desktop.lib.connectors.types import get_connectors_types
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/connectors/types.py", line 24, in <module>
	    from desktop.lib.exceptions_renderable import PopupException
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/exceptions_renderable.py", line 31, in <module>
	    import desktop.lib.django_util
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/django_util.py", line 42, in <module>
	    import desktop.lib.thrift_util
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/thrift_util.py", line 49, in <module>
	    from desktop.lib.thrift_.http_client import THttpClient
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/thrift_/http_client.py", line 26, in <module>
	    from desktop.lib.rest.http_client import HttpClient
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/rest/http_client.py", line 32, in <module>
	    from urllib3.contrib import pyopenssl
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/urllib3-1.25.8-py2.7.egg/urllib3/contrib/pyopenssl.py", line 46, in <module>
	    import OpenSSL.SSL
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/__init__.py", line 8, in <module>
	    from OpenSSL import crypto, SSL
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/crypto.py", line 16, in <module>
	    from OpenSSL._util import (
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/_util.py", line 6, in <module>
	    from cryptography.hazmat.bindings.openssl.binding import Binding
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/cryptography-2.9-py2.7-linux-x86_64.egg/cryptography/hazmat/bindings/openssl/binding.py", line 16, in <module>
	    from cryptography.hazmat.bindings._openssl import ffi, lib
	ImportError: /opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/cryptography-2.9-py2.7-linux-x86_64.egg/cryptography/hazmat/bindings/_openssl.so: undefined symbol: RSA_set0_factors
	Running '/opt/hue-4.8.0/hue/build/env/bin/hue collectstatic --noinput' with None
	Traceback (most recent call last):
	  File "/opt/hue-4.8.0/hue/build/env/bin/hue", line 11, in <module>
	    load_entry_point('desktop', 'console_scripts', 'hue')()
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/manage_entry.py", line 225, in entry
	    execute_from_command_line(sys.argv)
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/core/management/__init__.py", line 364, in execute_from_command_line
	    utility.execute()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/core/management/__init__.py", line 338, in execute
	    django.setup()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/__init__.py", line 27, in setup
	    apps.populate(settings.INSTALLED_APPS)
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/apps/registry.py", line 108, in populate
	    app_config.import_models()
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/Django-1.11.29-py2.7.egg/django/apps/config.py", line 202, in import_models
	    self.models_module = import_module(models_module_name)
	  File "/usr/lib64/python2.7/importlib/__init__.py", line 37, in import_module
	    __import__(name)
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/models.py", line 47, in <module>
	    from useradmin.models import User, Group, get_organization
	  File "/opt/hue-4.8.0/hue/apps/useradmin/src/useradmin/models.py", line 56, in <module>
	    from desktop.lib.connectors.models import _get_installed_connectors, Connector
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/connectors/models.py", line 29, in <module>
	    from desktop.lib.connectors.types import get_connectors_types
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/connectors/types.py", line 24, in <module>
	    from desktop.lib.exceptions_renderable import PopupException
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/exceptions_renderable.py", line 31, in <module>
	    import desktop.lib.django_util
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/django_util.py", line 42, in <module>
	    import desktop.lib.thrift_util
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/thrift_util.py", line 49, in <module>
	    from desktop.lib.thrift_.http_client import THttpClient
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/thrift_/http_client.py", line 26, in <module>
	    from desktop.lib.rest.http_client import HttpClient
	  File "/opt/hue-4.8.0/hue/desktop/core/src/desktop/lib/rest/http_client.py", line 32, in <module>
	    from urllib3.contrib import pyopenssl
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/urllib3-1.25.8-py2.7.egg/urllib3/contrib/pyopenssl.py", line 46, in <module>
	    import OpenSSL.SSL
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/__init__.py", line 8, in <module>
	    from OpenSSL import crypto, SSL
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/crypto.py", line 16, in <module>
	    from OpenSSL._util import (
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/pyOpenSSL-17.5.0-py2.7.egg/OpenSSL/_util.py", line 6, in <module>
	    from cryptography.hazmat.bindings.openssl.binding import Binding
	  File "/opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/cryptography-2.9-py2.7-linux-x86_64.egg/cryptography/hazmat/bindings/openssl/binding.py", line 16, in <module>
	    from cryptography.hazmat.bindings._openssl import ffi, lib
	ImportError: /opt/hue-4.8.0/hue/build/env/lib/python2.7/site-packages/cryptography-2.9-py2.7-linux-x86_64.egg/cryptography/hazmat/bindings/_openssl.so: undefined symbol: RSA_set0_factors
	
错误原因是系统中有多个OpenSSL的版本，因为自己编译安装的版本未创建对应的软连接，导致编译时查找的版本不正确。通过以下方式解决：

	# ln -s /usr/local/openssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
	# ln -s /usr/local/openssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
	# export LDFLAGS="-L/usr/local/openssl/lib"
	# export CPPFLAGS="-I/usr/local/openssl/include"
	# export PKG_CONFIG_PATH="/usr/local/openssl/lib/pkgconfig"
	
完成后再次编译安装成功。对于OpenSSL的处理可以参见我的另外一篇博文：[CentOS 6.8 安装 Python3 Could not build the ssl module](http://zhang-jc.github.io/2018/11/27/CentOS-6-8-%E5%AE%89%E8%A3%85-Python3-Could-not-build-the-ssl-module/)