title: HDFS ls 操作 OOM问题解决
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2018-03-19 23:37:24
---


如果一个目录下文件数量特别多（如 100W+），在执行 ls 操作时会出现 OOM 的错误，如下：

<!-- more -->

    Exception in thread "IPC Client connection to namenode/11.11.11.111:1111" java.lang.OutOfMemoryError: GC overhead limit exceeded
    at java.util.regex.Pattern.compile(Pattern.java:846)
    at java.lang.String.replace(String.java:2208)
    at org.apache.hadoop.fs.Path.normalizePath(Path.java:147)
    at org.apache.hadoop.fs.Path.initialize(Path.java:137)
    at org.apache.hadoop.fs.Path.<init>(Path.java:126)
    at org.apache.hadoop.dfs.DFSFileInfo.readFields(DFSFileInfo.java:141)
    at org.apache.hadoop.io.ObjectWritable.readObject(ObjectWritable.java:230)
    at org.apache.hadoop.io.ObjectWritable.readObject(ObjectWritable.java:166)
    at org.apache.hadoop.io.ObjectWritable.readObject(ObjectWritable.java:214)
    at org.apache.hadoop.io.ObjectWritable.readFields(ObjectWritable.java:61)
    at org.apache.hadoop.ipc.Client$Connection.run(Client.java:273)
    Exception in thread "main" java.lang.OutOfMemoryError: GC overhead limit exceeded
    at java.util.Arrays.copyOfRange(Arrays.java:3209)
    at java.lang.String.<init>(String.java:216)
    at java.lang.StringBuffer.toString(StringBuffer.java:585)
    at java.net.URI.toString(URI.java:1907)
    at java.net.URI.<init>(URI.java:732)
    at org.apache.hadoop.fs.Path.initialize(Path.java:137)
    at org.apache.hadoop.fs.Path.<init>(Path.java:126)
    at org.apache.hadoop.fs.Path.makeQualified(Path.java:296)
    at org.apache.hadoop.dfs.DfsPath.<init>(DfsPath.java:35)
    at org.apache.hadoop.dfs.DistributedFileSystem.listPaths(DistributedFileSystem.java:181)
    at org.apache.hadoop.fs.FsShell.ls(FsShell.java:405)
    at org.apache.hadoop.fs.FsShell.ls(FsShell.java:423)
    at org.apache.hadoop.fs.FsShell.ls(FsShell.java:423)
    at org.apache.hadoop.fs.FsShell.ls(FsShell.java:423)
    at org.apache.hadoop.fs.FsShell.ls(FsShell.java:399)
    at org.apache.hadoop.fs.FsShell.doall(FsShell.java:1054)
    at org.apache.hadoop.fs.FsShell.run(FsShell.java:1244)
    at org.apache.hadoop.util.ToolBase.doMain(ToolBase.java:187)
    at org.apache.hadoop.fs.FsShell.main(FsShell.java:1333)

这是因为 HDFS Client 进程的 heapsize  默认为 1G，如果目录下文件太多就会出现 OOM。这并不是一个 Bug，解决这个问题只需要将 HDFS Client 进程的 heapsize 设置大一些就可以。设置方法如下：

    $ export HADOOP_CLIENT_OPTS="-XX:-UseGCOverheadLimit -Xmx4096m"
    $ hadoop fs -ls /path/
