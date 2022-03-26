---
title: Docker修改已有容器的端口映射
date: 2022-03-26 20:00:12
tags:
- Docker
- 云计算
categories:
- 云计算
- Docker
---

此文是在现在容器实例上修改端口映射，并不希望创建新的容器，这样可以保持原有容器中的数据。

方法是修改容器目录下 hostconfig.json 配置文件中的 PortBindings 配置项内容。如下：

```Json
"PortBindings":{"8080/tcp":[{"HostIp":"","HostPort":"8080"}]}
```
前一个数字是容器端口，后一个是宿主机端口。将宿主机的8080端口映射到容器的8080端口。如修改现有端口映射则把端口号改掉就OK。