title: '[: ==: unary operator expected'
tags:
  - Linux
  - Shell
categories:
  - 语言
  - Shell
date: 2016-06-26 14:04:01
---

Shell 脚本报错：“line 10: [: =: unary operator expected”。根据提示信息找到报错的程序是：

    if [ $OPERATION == "scp" ]; then

报错原因是变量 OPERATION 的值为空，那么程序就变成了：

    if [ == "scp" ]; then

显然 [ 和 "scp" 不相等并且缺少了 [ 符号，所以报了这样的错误。检查程序发现是 OPERATION 在声明的时候写错导致的。这样的错误还是很隐蔽的，所以为了增强程序的健壮性可以用下面的写法：

    if [ "${OPERATION}x" == "scpx" ]; then
