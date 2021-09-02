---
title: Connection to pypi.python.org timed out
date: 2021-09-02 10:49:32
tags:
- Python3
categories:
- 语言
- Python3
---

如果出现以下错误信息说明采用了默认的pypi源（国外的pypi源），这个很容易出现这种连接超时的问题，应当采用国内的镜像源。下面以阿里提供的镜像源为例。

一般配置参照阿里官网说明：[PyPI 镜像](https://developer.aliyun.com/mirror/pypi?spm=a2c6h.13651102.0.0.3e221b11xoeg2M)

下面主要说明Docker中如何设置。首先创建pip.conf文件并添加以下内容：

```toml
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

将以下内容添加至Dockerfile中：

```text
RUN mkdir /root/.pip

COPY pip.conf /root/.pip
```