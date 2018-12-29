title: Linux 命令行导出 Emacs ORG 文档为 HTML
date: 2018-12-28 01:44:30
tags:
- Linux
- Emacs
categories:
- 开发工具
- Emacs
---

Emacs 版本 25.2。使用以下命令将 org 文档导出 html：

    emacs {orgFile} --batch --eval "(require 'ox)" --eval "(org-html-export-to-html)"

批量导出目录下的 org 文档：

    for orgFile in `ls *.org`
    do
      emacs ${orgFile} --batch --eval "(require 'ox)" --eval "(org-html-export-to-html)"
    done
