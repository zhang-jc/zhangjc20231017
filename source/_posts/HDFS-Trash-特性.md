title: HDFS Trash 特性
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-03-06 17:22:20
---

### HDFS Trash 特性

如果启用 Trash 配置，通过 [FS Shell](http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) 删除的文件不会立即从 HDFS 上移除。HDFS 将这些文件移动到一个回收站目录（每个用户在 /user/<username>/.Trash 下都有他自己的回收站目录）。只要这些文件还在回收站中，可以很快地进行恢复。

<!-- more -->

最近删除的文件移动到当前的回收站目录（/user/<username>/.Trash/Current），在一个可配置的时间间隔，HDFS 对当前回收站目录中的文件创建检查点（在 /user/<username>/.Trash/<date>下），并删除过期的检查点。参见关于回收站检查点的 [FS Shell  expunge 命令](http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/FileSystemShell.html#expunge)。

当文件在回收站中过期后，NameNode 会从 HDFS 命名空间删除这些文件。一个文件的删除会引起跟这个文件相关联的块被释放。注意，在文件被用户删除的时间和 HDFS 中与之相关的可用空间增长时间之间存在一个可感知的时间延迟。

### [expunge 命令](http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/FileSystemShell.html#expunge)

用法：hadoop fs -expunge

从回收站目录中永久删除比保留阀值旧的检查点中的文件，并创建新的检查点。

当检查点被创建，当前被删除的在回收站中的文件被移动到该检查点下。比 fs.trash.checkpoint.interval 配置旧的检查点中的文件在下一次调用 -expunge 命令时将被永久的删除。

如果文件系统支持这个特性，用户可以通过参数 fs.trash.checkpoint.interval（在 core-site.xml 中）来配置周期性创建和删除检查点。这个值应该比 fs.trash.interval 小或者相等。

### fs.trash.interval 参数

分钟数，当超过这个分钟数后检查点会被删除。如果为 0，Trash 特性被禁用。这个选项可以在服务器和客户端都设置。如果服务端 Trash 被禁用，那么会检查使用客户端配置。如果启用服务端 Trash，那么使用服务器配置的参数值，并忽略客户端配置的参数值。

### fs.trash.checkpoint.interval 参数

检查点之间的间隔分钟数。应该比 fs.trash.interval 小或者相等。如果为 0，该参数的值被设置为 fs.trash.interval 的参数值。每次检查点程序运行时，它会从当前回收站创建一个新的检查点，并移除超过 fs.trash.interval 配置参数分钟数以前的检查点。

### 示例

（1）创建两个目录：

    $ hadoop fs -mkdir -p /user/hadoop/delete/test1
    $ hadoop fs -mkdir -p /user/hadoop/delete/test2
    $ hadoop fs -ls /user/hadoop/delete/
    Found 2 items
    drwxr-xr-x   - hadoop supergroup          0 2017-03-06 18:43 /user/hadoop/delete/test1
    drwxr-xr-x   - hadoop supergroup          0 2017-03-06 18:46 /user/hadoop/delete/test2

（2）删除目录 test1，提示信息显示目录被移动到回收站目录：

    $ hadoop fs -rm -r /user/hadoop/delete/test1
    17/03/06 18:49:02 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 4320 minutes, Emptier interval = 1440 minutes.
    Moved: 'hdfs://frin-namenode1:9000/user/hadoop/delete/test1' to trash at: hdfs://frin-namenode1:9000/user/hadoop/.Trash/Current

（3）使用 skipTrash 选项删除目录 test2，目录不会移动到回收站。它会从从 HDFS 被彻底删除。

    $ hadoop fs -rm -r -skipTrash /user/hadoop/delete/test2
    Deleted /user/hadoop/delete/test2

（4）查看回收站目录可以看到只包含 test1 目录：

    $ hadoop fs -ls /user/hadoop/.Trash/Current/user/hadoop/delete/
    Found 1 items
    drwxr-xr-x   - hadoop supergroup          0 2017-03-06 18:43 /user/hadoop/.Trash/Current/user/hadoop/delete/test1

### 特别说明

1. 回收站用的数据保存的理论最长时间约等于 fs.trash.interval 参数的 2 倍；

2. 如果 NameNode 重启，会重新开始计时以确定检查点程序的执行时间。