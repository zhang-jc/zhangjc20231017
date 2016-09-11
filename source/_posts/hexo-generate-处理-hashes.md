title: 'hexo generate 处理 #'
tags:
  - Hexo
  - Markdown
categories:
  - 开发
  - Node.js
date: 2016-09-11 21:28:31
---

今天在发布一篇博文的时候执行 hexo generate 发生错误，错误信息如下：

    $ hexo generate
    INFO  Start processing
    FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
    Template render error: (unknown path)
      Error: expected end of comment, got end of file

想到刚才文章内容中有 # 字符作为普通字符出现，但并没有做转义处理，包含 # 字符的内容如下：

    ${#v1}

经过实际测试确实是因为 # 导致的。

首先想到用 Markdown 转义字符 \ 进行转义，但问题依旧。

hexo generate 目标是将 .md 文件内容转为 html 文件，html 可以对字符编码。从网上查了下 # 的 html 编码为 &\#35; 替换后问题解决。
    