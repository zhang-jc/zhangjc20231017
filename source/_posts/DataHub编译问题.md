---
title: DataHub编译问题
date: 2022-07-20 09:27:45
tags:
- Datahub
- 元数据
- 数据治理
- 数据目录
---

# 环境信息

OS：Linux frin 5.15.0-41-generic #44~20.04.1-Ubuntu SMP Fri Jun 24 13:27:29 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

# sasl/sasl.h: 没有那个文件或目录

```
# apt get install libsasl2-dev libsasl2-modules
```

# lber.h: 没有那个文件或目录

···
sudo apt-get install python-dev libldap2-dev libssl-dev
···