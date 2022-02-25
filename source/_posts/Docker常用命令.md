---
title: Docker常用命令
date: 2022-02-24 14:34:53
tags:
- 云计算
- Docker
categories:
- 云计算
- Docker
---

# 指定名称启动容器

```Shell
docker run --name Redis -it centos bash
```

# 列出所有容器

```Shell
docker ps -a

CONTAINER ID   IMAGE    COMMAND    CREATED        STATUS                       PORTS     NAMES
8aeb92ae184b   centos   "bash"     4 hours ago    Exited (130) 5 minutes ago             Redis
```

# 启动已存在的容器

```Shell
docker start Redis
```

# 连接已运行的容器

```Shell
docker exec -it Redis bash
```
