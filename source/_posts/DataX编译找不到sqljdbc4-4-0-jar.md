---
title: DataX编译找不到sqljdbc4-4.0.jar
date: 2021-11-09 11:38:01
tags:
- DataX
- 大数据
categories:
- 大数据
- DataX

---
这个问题的发生是因为我从GitHub克隆并转存到内部GitLab后发生的。检查转存后GitLab上项目源文件发现确实不存在。原因是从GitHub克隆源项目中.gitignore配置文件中配置了忽略规则，所以这些Jar不会提交到转存后的项目中。.gitignore中需要修改两个地方。

- 第一处：

修改前：

```text
*.jar
```

修改后：

```text
*.jar
!sqljdbc4-4.0.jar
```

- 第二处：

修改前：

```text
lib/
```

修改后：

```text
lib/
!sqlserverreader/src/main/lib/
```
