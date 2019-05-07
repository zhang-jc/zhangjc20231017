title: lftp 的 sftp 使用时遇到的坑
date: 2019-05-07 21:37:40
tags:
- Linux
categories:
- 操作系统
- Linux
---

今天在使用 lftp 的 sftp 时候一直处于 connecting 状态，并且没有其他提示信息。状态如下图所示：
![lftp sftp](/uploads/20190507/lftp-sftp.png)

<!-- more -->

导致这个问题的原因是，在 sftp 第一次连接服务器的时候需要接收服务端服务器的 fingerprint，这个过程需要用户通过输入进行确认，如下图所示。但是这些提示信息被 lftp 掩盖了，所以 connecting 状态其实是 sftp 一直在等待用户的输入。解决方法是先在命令行使用 sftp 连接一次服务器，然后在使用 lftp。
！[Are you sure you want to continue connecting](/uploads/20190507/continue-connecting.png)