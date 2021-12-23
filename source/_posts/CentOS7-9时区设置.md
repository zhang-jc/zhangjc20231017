---
title: CentOS7.9时区设置
date: 2021-12-23 15:25:53
tags:
- 操作系统
- CentOS
- Docker
categories:
- 操作系统
- CentOS
---

执行以下命令：

```Shell
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo "Asia/shanghai" > /etc/timezone
```

该方法在Docker容器中一样适用。