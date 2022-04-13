---
title: Linux下^M处理
date: 2022-04-13 13:52:58
tags:
- Shell
- Linux
categories:
- 操作系统
- Linux
---

此处针对shell脚本运行时报^M引起的异常，但是通过vi查看未显示^M的情况下如何处理。针对这种情况可以使用sed命令进行替换，如下：

```Shell
sed -i 's/^M//g' file1.txt
```

> 注意：^M其实是CTRL+V与CTRL+M的组合。