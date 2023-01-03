---
title: Ubuntu系统中IPhone 6s照片路径
date: 2023-01-03 12:47:04
tags:
- Ubutun
---

通过find搜索发现IPone 6s接入Ubuntu后照片位置规则如下：

```Shell
$ cd /run/user/1000/gvfs/gphoto2:host=Apple_Inc._iPhone_314f6be727cfce5f1f77980808a6bfb62b87bf63
$ ls
DCIM
```
