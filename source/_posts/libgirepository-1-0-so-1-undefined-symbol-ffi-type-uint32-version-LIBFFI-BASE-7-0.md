---
title: >-
  libgirepository-1.0.so.1: undefined symbol: ffi_type_uint32, version
  LIBFFI_BASE_7.0
date: 2023-05-17 22:40:43
tags:
---

```Python
$ python3
Python 3.10.9 (main, Mar  1 2023, 18:23:06) [GCC 11.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import gi
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/username/anaconda3/lib/python3.10/site-packages/gi/__init__.py", line 40, in <module>
    from . import _gi
ImportError: /lib/x86_64-linux-gnu/libgirepository-1.0.so.1: undefined symbol: ffi_type_uint32, version LIBFFI_BASE_7.0
```

```Shell
export LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libffi.so.7
```