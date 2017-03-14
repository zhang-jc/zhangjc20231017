title: Hadoop 2.7.3 文档 expunge 命令排版错误
tags:
  - Hadoop
categories:
  - 大数据
  - Hadoop
date: 2017-03-12 06:57:58
---

在 Hadoop 2.7.3 版本的官方文档中 File System Shell 中的 expunge 命令描述中以下位置存在错误：

When checkpoint is created, recently deleted files in trash are moved under the checkpoint. Files in checkpoints older than <span style="color:red;">**fs.trash.checkpoint.interval**</span> will be permanently deleted on the next invocation of -expunge command.

<!-- more -->

红色字体标注的参数应该是 <span style="color:red;">**fs.trash.interval**</span>。这个排版错误已经在  2.8.0 和 3.0.0-alpha1 中修复，参见 Jira Issue：<https://issues.apache.org/jira/browse/HADOOP-12675>