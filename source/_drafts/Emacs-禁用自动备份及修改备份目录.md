title: Emacs 禁用自动备份及修改备份目录
tags:
- Emacs
categories:
- 开发工具
- Emacs
---

### Emacs 禁用自动备份

在 ~/.emacs 文件中添加配置：

    ;; disable auto backup
	(setq make-backup-files nil)

### 修改备份目录

在 ~/.emacs 文件中添加配置：

    ;; set backup directory to ~/.backups
    (setq backup-directory-alist (quote (("." . "~/.backups"))))
