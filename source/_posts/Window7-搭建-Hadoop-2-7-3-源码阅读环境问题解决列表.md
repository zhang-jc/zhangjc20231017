title: Window7 搭建 Hadoop-2.7.3 源码阅读环境问题解决列表
tags:
  - 大数据
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-02-11 18:47:51
---

#### 环境说明

- Windows 7
- java version "1.7.0_80"
- Apache Maven 3.2.3
- ProtocolBuffer 2.5.0
- cmake version 3.7.2 win64 x64
- Windows SDK 7.1

构建过程参照源代码目录下 BUILDING.txt 说明文件中的“Building on Windows”中的内容；以及我之前的文章“[搭建 Hadoop 源代码阅读环境](http://zhang-jc.github.io/2016/08/29/%E6%90%AD%E5%BB%BA-Hadoop-%E6%BA%90%E4%BB%A3%E7%A0%81%E9%98%85%E8%AF%BB%E7%8E%AF%E5%A2%83/)”。

<!-- more -->

#### LINK : fatal error LNK1123: 转换到 COFF 期间失败

错误信息：LINK : fatal error LNK1123: 转换到 COFF 期间失败: 文件无效或损坏 [C:\hadoop-2.7.3\hadoop-common-project\hadoop-common\src\main\winutils\winutils.vcxproj]

解决方法：安装 VS SP1 后重现编译。VS SP1 下载地址：<https://www.microsoft.com/en-us/download/details.aspx?id=23691>

#### fatal error C1083: Cannot open include file: 'ammintrin.h'

错误信息：C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\include\intrin.h(26): fatal error C1083: Cannot open include file: 'ammintrin.h': No such file or directory [D:\hadoop-2.7.3\hadoop-common-project\hadoop-common\src\main\native\native.vcxproj]

解决方法：下载 ammintrin.h 文件，然后放置到目录 C:\Program Files (x86)\Microsoft Visual Studio 10.0\VC\include\ 下，然后重新编译。下载地址：<http://wenku.baidu.com/link?url=a1MMc5tWY2SnczBiVLk0MiKFfnSpRWGRHKNIwS3vwCNCCAmTIT7fbyayzu1gJrjY4-0UCs7FLbXvMLItmLa-BPZOqHOQxy5cs--X_vSpYam>

#### error : You are attempting to build a Win32 application from an x64 environment

错误信息：C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\Platforms\Win32\PlatformToolsets\Windows7.1SDK\Microsoft.Cpp.Win32.Windows7.1SDK.targets(20,5): error : You are attempting to build a Win32 application from an x64 environment. If using the Windows 7.1 SDK build environment, type setenv /x86 [D:\hadoop-2.7.3\hadoop-common-project\hadoop-common\src\main\winutils\libwinutils.vcxproj]

解决方法：这个错误是由于没有按照 BUILDING.txt 中的说明编译之前必须设置平台环境变量导致。编译前先用下面的命令设置平台环境变量：

    set Platform=x64 (when building on a 64-bit system)
    set Platform=Win32 (when building on a 32-bit system)

#### CMake Error

错误信息：[exec] CMake Error: Could not create named generator Visual Studio 10 Win64

解决方法：最开始我安装的是 cmake 2.6.4，因为这个版本没有 win64 的版本，所以出错。卸载重新安装 cmake-3.7.2-win64-x64 版本，重新编译成功。cmake 各版本下载地址：<https://cmake.org/files/>

> 网上也有类似错误是因为系统中装了 cygwin，且 cygwin 在环境变量 path 中的位置在 cmake 之前。如果是这种情况则将 cmake 目录路径在环境变量 path 中的位置移到 cygwin 前面就解决了。
