---
title: wget下载文件重命名
date: 2022-02-24 14:03:19
tags:
- Linux
categories:
- 操作系统
- Linux
---

```Text
$ wget -c 'https://camel-builds.s3.amazonaws.com/ActiveTcl/x86_64-linux-glibc-2.17/20210816T193804Z/ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAQ5FYQM547I2EFPRW%2F20220224%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220224T060023Z&X-Amz-Expires=21600&X-Amz-SignedHeaders=host&X-Amz-Signature=6dc3ab58057785a5a5bd4c0b12ab55e8a31a14a59b7f56f8eaa0915263502e3b'
--2022-02-24 06:01:04--  https://camel-builds.s3.amazonaws.com/ActiveTcl/x86_64-linux-glibc-2.17/20210816T193804Z/ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAQ5FYQM547I2EFPRW%2F20220224%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220224T060023Z&X-Amz-Expires=21600&X-Amz-SignedHeaders=host&X-Amz-Signature=6dc3ab58057785a5a5bd4c0b12ab55e8a31a14a59b7f56f8eaa0915263502e3b
Resolving camel-builds.s3.amazonaws.com (camel-builds.s3.amazonaws.com)... 52.216.240.84
Connecting to camel-builds.s3.amazonaws.com (camel-builds.s3.amazonaws.com)|52.216.240.84|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 27637315 (26M) [application/gzip]
ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAQ5FYQM547I2EFPRW%2F20220224%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220224T060023Z&X-Amz-Expires=21600&X-Amz-SignedHeaders=host&X-Amz-Signature=6dc3ab58057785a5a5bd4c0b12ab55e8a31a14a59b7f56f8eaa0915263502e3b: File name too long

Cannot write to 'ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAQ5FYQM547I2EFPRW%2F20220224%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220224T060023Z&X-Amz-Expires=21600&X-Amz-SignedHeaders=host&X-Amz-Signature=6dc3ab58057785a5a5bd4c0b12ab55e8a31a14a59b7f56f8eaa0915263502e3b' (Success).
```

对要下载的文件重命名解决：

```Shell
$ wget -c 'https://camel-builds.s3.amazonaws.com/ActiveTcl/x86_64-linux-glibc-2.17/20210816T193804Z/ActiveTcl-8.6.11.1.0000-x86_64-linux-glibc-2.17-e4e2f327.tar.gz?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAQ5FYQM547I2EFPRW%2F20220224%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20220224T060023Z&X-Amz-Expires=21600&X-Amz-SignedHeaders=host&X-Amz-Signature=6dc3ab58057785a5a5bd4c0b12ab55e8a31a14a59b7f56f8eaa0915263502e3b' -O tcl-8.6.tar.gz
```