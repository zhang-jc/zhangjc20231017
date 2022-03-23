---
title: MySQL Workbench CSV导入编码问题
date: 2022-03-21 13:50:43
tags:
- MySQL
categories:
- 数据库
- Mysql
---

异常信息：

```Text
Unhandled exception: 'gbk' codec can't decode byte 0xa2 in position 43; illegal multibyte sequence
```

如图：![MySQL Workbench Error](/images/20220321/mysql-workbench-codec-error.jpg)

因为Windows环境下Mysql Workbench编码器只有gbk，而csv文件是utf-8编码的，所以导致该问题。如图：![CSV UTF8](/images/20220321/csv-utf8.png)

将csv编码改为gbk后再次导入成功。如图：![CSV UTF8](/images/20220321/csv-gbk.png)

另外一种思路：为Mysql Workbench安装UTF-8编码器。这种方法没有实际进行尝试，感兴趣的话可以探索下^_^