title: Ubuntu 20.04安装Protocol Buffers 2.5.0
date: 2021-07-01 11:46:27
tags:
- Ubuntu
- Linux
- Google
- Protocol Buggers
categories:
- 开发
- 环境搭建
---

# 安装过程

Protocol Buffers 2.5.0源码下载：https://github.com/protocolbuffers/protobuf/tree/v2.5.0。下载并解压。

将autogen.sh文件中以下内容：

```Shell
  curl http://googletest.googlecode.com/files/gtest-1.5.0.tar.bz2 | tar jx
  mv gtest-1.5.0 gtest
```

替换为：

```Shell
  wget https://github.com/google/googletest/archive/release-1.5.0.tar.gz
  tar xzvf release-1.5.0.tar.gz
  mv googletest-release-1.5.0 gtest
```

执行以下命令进行安装：

```Shell
./autogen.sh
./configure
make
make check
make install
```

# 安装问题
## 问题现象

安装完成后验证出现以下错误：

```Shell
$ protoc --version
protoc: error while loading shared libraries: libprotoc.so.8: cannot open shared object file: No such file or directory
```

## 问题解决

- 使用find命令找到libprotoc.so.8文件位置。

```Shell
$ cd /usr/local/
$ find . -name libprotoc.so.8
./lib/libprotoc.so.8
```

- 创建/etc/ld.so.conf.d/libprotobuf.conf文件，并输入以下内容：

```Text
/usr/local/lib
```

- 执行命令：sudo ldconfig