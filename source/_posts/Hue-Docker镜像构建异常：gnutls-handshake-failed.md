---
title: Hue Docker镜像构建异常：gnutls_handshake() failed
date: 2021-09-03 10:02:25
tags:
- Hue
- Docker
- GitHub
- 大数据
categories:
- 大数据
- Hue
---

错误信息如下：

```Log
  Cloning https://github.com/gethue/PyHive to /tmp/pip-req-build-86w_hwe4
  Running command git clone -q https://github.com/gethue/PyHive /tmp/pip-req-build-86w_hwe4
  fatal: unable to access 'https://github.com/gethue/PyHive/': gnutls_handshake() failed: The TLS connection was non-properly terminated.
WARNING: Discarding git+https://github.com/gethue/PyHive. Command errored out with exit status 128: git clone -q https://github.com/gethue/PyHive /tmp/pip-req-build-86w_hwe4 Check the logs for full command output.
ERROR: Command errored out with exit status 128: git clone -q https://github.com/gethue/PyHive /tmp/pip-req-build-86w_hwe4 Check the logs for full command output.
The command '/bin/sh -c ./build/env/bin/pip install --no-cache-dir   psycopg2-binary   django_redis==4.11.0   flower   git+https://github.com/gethue/PyHive   git+https://github.com/bryanyang0528/ksql-python   pydruid   pybigquery   elasticsearch-dbapi   pyasn1==0.4.1   python-snappy==0.5.4   threadloop   sqlalchemy-clickhouse   infi.clickhouse_orm==1.0.4' returned a non-zero code: 1
```

网上大都是说因为代理的问题，对我这个场景没用。通过搜索找到一个很好的代理：[https://mirror.ghproxy.com](https://mirror.ghproxy.com)

desktop/core/requirements.txt中以下位置：

```Text
git+https://github.com/gethue/django-babel.git
git+https://github.com/gethue/django-mako.git
```

改为：

```Text
git+https://mirror.ghproxy.com/https://github.com/gethue/django-babel.git
git+https://mirror.ghproxy.com/https://github.com/gethue/django-mako.git
```

tools/docker/hue/Dockerfile中以下位置：

```Text
  git+https://github.com/gethue/PyHive \
  # pyhive \
  #ksql \
  git+https://github.com/bryanyang0528/ksql-python \
```

改为：

```Text
  git+https://mirror.ghproxy.com/https://github.com/gethue/PyHive \
  # pyhive \
  #ksql \
  git+https://mirror.ghproxy.com/https://github.com/bryanyang0528/ksql-python \
```