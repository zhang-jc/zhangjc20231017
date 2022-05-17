---
title: git status中文乱码
date: 2022-05-17 10:28:28
tags:
- Git
---

我的环境信息：Ubuntu20.04、git 2.25.1

问题现象如下：

```Shell
$ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。

未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）
	"source/_posts/git-status\344\270\255\346\226\207\344\271\261\347\240\201.md"

提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）
```

执行以下命令解决：

```Shell
$ git config --global core.quotepath false
$ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。

未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）
	source/_posts/git-status中文乱码.md

提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）
```