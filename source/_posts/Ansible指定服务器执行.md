---
title: Ansible指定服务器执行
date: 2021-10-19 23:19:03
tags:
---

Inventory内容如下：

```INI
[namenode]
10.0.0.42
10.0.0.51

[resourcemanager]
10.0.0.55
10.0.0.65
```

如果只需要对10.0.0.55这台服务进行操作应该如何做呢？如下：

```Shell
ansible -i hosts "10.0.0.55" -m shell -a "ls -l /"
```

详细信息参见Ansible官方文档：[https://docs.ansible.com/ansible/latest/user_guide/intro_patterns.html#intro-patterns](https://docs.ansible.com/ansible/latest/user_guide/intro_patterns.html#intro-patterns)