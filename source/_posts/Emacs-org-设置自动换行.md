title: Emacs org 设置自动换行
date: 2019-11-02 16:58:44
tags:
- Emacs
categories:
- 开发工具
- Emacs
---

在 Emacs 配置文件 .emacs 中添加以下配置：

    (add-hook 'org-mode-hook (lambda () (setq truncate-lines nil)))
