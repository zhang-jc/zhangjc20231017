title: HDFS 快照
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2017-04-12 18:07:27
---

### 概述

HDFS 快照是只读的某个时间点的文件系统拷贝。可以在一个子树或者整个文件系统上执行快照。一般使用快照的场景有数据备份、防止用户错误及灾难恢复。

<!-- more -->

HDFS 快照的实现是很有效的：

- 快照的创建是瞬间的：代价是 O(1)，除了索引点（inode）查找时间。
- 只有发生跟快照相关的修改时才需要额外的内存：内存使用是 O(M)，M 是修改的文件/目录的数目。
- DataNode 上的块不会拷贝：快照文件记录块列表及文件的大小。不会拷贝数据。
- 快照不会对常规的 HDFS 操作有不利的影响：修改是以时间倒序排列的，因此当前的数据可以直接访问。快照数据是通过从当前数据减去修改来计算的。

#### 快照表目录

快照表可以在任何目录上创建，只要目录被设置为快照表。一个快照表目录可以同时容纳 65536 个快照。快照表目录的数量没有限制。管理员可以设置任何目录为快照表。如果一个快照表目录下存在快照，那么这个目录在所有的快照被删除前不能被删除也不能重命名。嵌套的快照表目录当前是不允许的。就是说，如果一个目录的一个上级目录/下级目录是快照表目录，那么这个目录不能被设置为快照表。

#### 快照路径

对于一个快照表目录，路径组件“.snapshot”用来访问它的快照。假设 /foo 是一个快照表目录，/foo/bar 是 /foo 中的一个文件/目录，/foo 有一个快照 s0。那么，路径

    /foo/.snapshot/s0/bar

指向 /foo/bar 的快照拷贝。普通 API 和 CLI 可以用“.snapshot”路径工作。下面是一些例子。

- 列出一个快照表目录下所有的快照：

    hdfs dfs -ls /foo/.snapshot

- 列出快照 s0 中的文件：

    hdfs dfs -ls /foo/.snapshot/s0

- 从快照 s0 中拷贝一个文件：

    hdfs dfs -cp -ptopax /foo/.snapshot/s0/bar /tmp

注意，这个例子使用了保持选项保持时间戳、属主关系、权限、ACL 和 XAttr。

### 用快照升级 HDFS 到某个版本

HDFS 快照特性引入了一个新的保留路径名用来与快照交互：.snapshot。当从 HDFS 的一个旧版本升级时，存在的 .snapshot 路径名称需要先重命名或删除以避免与保留路径冲突。参见 [HDFS 用户指南](http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#Upgrade_and_Rollback)升级一节获取更多信息。

#### 快照选项

##### 管理员选项

这一节描述的操作需要超级用户权限。

###### 允许快照

允许创建一个目录的快照。如果操作成功完成，这个目录变为快照表。

- 命令：

    hdfs dfsadmin -allowSnapshot &lt;path&gt;

- 参数：

path：快照表目录的路径。

也可以参见 HdfsAdmin 中对应的 Java API void allowSnapshot(Path path)。

###### 禁止快照

禁止一个目录创建快照。在禁止前这个目录的所有快照必须删除。

- 命令：

    hdfs dfsadmin -disallowSnapshot &lt;path&gt;

- 参数：

path：快照表目录的路径。

也可以参见 HdfsAdmin 中对应的 Java API void disallowSnapshot(Path path)。

##### 用户操作

这一节描述用户操作。注意，HDFS 超级用户可以执行所有操作而不需要各个操作中要求的权限需求。

###### 创建快照

创建一个快照表目录的快照。这个操作需要快照表目录属主的权限。

- 命令：

    hdfs dfs -createSnapshot &lt;path&gt; [&lt;snapshotName&gt;]

- 参数：

path：快照表目录的路径。

snapshotName：快照名字，这是个可选参数。当省略这个参数时，会用时间戳以格式“'s'yyyyMMdd-HHmmss.SSS”生成默认名称，例如：s20130412-151029.033。

也可以参见 [FileSystem](http://hadoop.apache.org/docs/r2.7.3/api/org/apache/hadoop/fs/FileSystem.html) 中对应的 Java API Path createSnapshot(Path path) 和 Path createSnapshot(Path path, String snapshotName)。这些方法返回快照的路径。

###### 删除快照

从一个快照表目录中删除快照。这个操作需要快照表目录属主的权限。

- 命令：

    hdfs dfs -deleteSnapshot &lt;path&gt; &lt;snapshotName&gt;

- 参数：

path：快照表目录的路径。

snapshotName：快照名称。

也可以参见 [FileSystem](http://hadoop.apache.org/docs/r2.7.3/api/org/apache/hadoop/fs/FileSystem.html) 中对应的 Java API  void deleteSnapshot(Path path, String snapshotName)。

###### 重命名快照

重命名一个快照。这个操作需要快照表目录属主的权限。

- 命令：

    hdfs dfs -renameSnapshot &lt;path&gt; &lt;oldName&gt; &lt;newName&gt;

- 参数：

path：快照表目录的路径。

oldName：原快照名称。

newName：新快照名称。

也可以参见 [FileSystem](http://hadoop.apache.org/docs/r2.7.3/api/org/apache/hadoop/fs/FileSystem.html) 中对应的 Java API  void renameSnapshot(Path path, String oldName, String newName)。

###### 获取快照表目录列表

获取当前用户有权限的所有快照表目录。

- 命令：

    hdfs lsSnapshottableDir

- 参数：无

也可以参见 DistributedFileSystem 中对应的 Java API SnapshottableDirectoryStatus[] getSnapshottableDirectoryListing()。

###### 获取快照差异报告

获取两个快照间的差异。这个操作需要两个快照中所有文件/目录读访问权限。

- 命令：

    hdfs snapshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;

- 参数：

path：快照表目录的路径。

fromSnapshot：开始快照的名称。

toSnapshot：结束快照的名称。

- 结果：

&#43;：文件/目录被创建。  
&minus;：文件/目录被删除。  
M：文件/目录被修改。
R：文件/目录被重命名。

重命名实体表明一个文件/目录被重命名但依然在同一个快照表目录中。如果一个文件/目录被重命名到快照表目录的外部，那么这个文件/目录报告为被删除。一个文件/目录从外部重命名到快照表中会报告为新创建。

快照差异报告不保证相同的操作序列。例如，重命名目录“/foo”为“/foo2”，然后向文件“/foo2/bar”追加数据，差异报告将是：

    R. /foo -> /foo2
    M. /foo/bar

换言之，被重命名目录下的文件/目录的修改会用被重名前原来的路径报告（上面例子中的“/foo/bar”）。

也可以参见 DistributedFileSystem 中对应的 Java API SnapshotDiffReport getSnapshotDiffReport(Path path, String fromSnapshot, String toSnapshot)。