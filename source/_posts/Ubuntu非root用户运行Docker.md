---
title: Ubuntu非root用户运行Docker
date: 2022-07-21 09:27:43
tags:
- 操作系统
- Ubuntu
- Docker
---

创建Docker用户组。

```
sudo groupadd docker
```

将用户添加至docker用户组：

```
sudo usermod -G docker -a ${USERNAME}
```
