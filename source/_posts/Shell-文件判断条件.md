title: Shell 文件判断条件
tags:
  - Shell
categories:
  - 语言
  - Shell
date: 2016-09-11 22:03:28
---

1. -e filename：如果 filename 存在，则为真。
2. -d filename：如果 filename 为目录，则为真。

<!-- more -->

3. -f filename：如果 filename 为常规文件，则为真。
4. -L filename：如果 filename 为符号链接，则为真。
5. -r filename：如果 filename 可读，则为真。
6. -w filename：如果 filename 可写，则为真。
7. -x filename：如果 filename 可执行，则为真。
8. -s filename：如果文件长度不为 0，则为真。
9. -h filename：如果文件是软链接，则为真。
10. filename1 -nt filename2：如果 filename1 比 filename2 新，则为真。
11. filename1 -ot filename2：如果 filename1 比 filename2 旧，则为真。