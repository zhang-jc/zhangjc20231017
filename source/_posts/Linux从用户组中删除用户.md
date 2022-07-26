---
title: Linux从用户组中删除用户
date: 2022-07-22 10:39:54
tags:
- Linux
---

使用 gpasswd，如下：

```Shell
sudo gpasswd -d test docker
```

将test用户从docker用户组中删除。其他用法参见gpasswd手册。