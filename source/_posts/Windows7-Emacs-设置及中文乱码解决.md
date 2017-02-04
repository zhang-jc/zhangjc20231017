title: Windows7 Emacs 设置及中文乱码解决
tags:
  - Emacs
categories:
  - 开发工具
  - Emacs
date: 2017-01-08 19:40:06
---

### 环境说明

- Windows7
- GNU Emacs 25.1.1
- 安装路径：D:/apps/emacs/

<!-- more -->

### 配置 Emacs

在 Windows7 下安装完 Emacs 后，默认情况下 Emacs 不会在一启动的时候就生成 .emacs 配置文件和 .emacs.d 目录。为了生成配置文件需要做如下操作：

- 启动 Emacs
- 在 Options 菜单中随便更改一下设置，如选中“Highlight Matching Parentheses”
- 点 Save Options 保存

生成的 .emacs 目录还是在 C:\Users\<username>\AppData\Roaming 下。打开 C:\Users\<username>\AppData\Roaming\.emacs 配置文件，添加如下配置：

    (load-file "D:/apps/emacs/.emacs")

Emacs在启动的时候会加载 C:\Users\<username>\AppData\Roaming\.emacs 这个配置文件，而该文件又加载 D:/apps/emacs/.emacs 配置文件。这样不用进入 C:\Users\<username>\AppData\Roaming\.emacs 这个冗的路径了。

使用以下配置修改 HOME PATH 配置项：

    (setenv "HOME" "D:/emacs-23.2")
    (setenv "PATH" "D:/emacs-23.2")
    ;;set the default file path
    (setq default-directory "~/")
    (setq load-path (cons "~/.emacs.d/elisp" load-path))

### 解决中文乱码

在 .emacs 文件中加入如下配置：

    ;; 编码设置 begin
    (set-language-environment 'Chinese-GB)
    
    ;; default-buffer-file-coding-system变量在emacs23.2之后已被废弃，使用buffer-file-coding-system代替
    (set-default buffer-file-coding-system 'utf-8-unix)
    (set-default-coding-systems 'utf-8-unix)
    (setq-default pathname-coding-system 'euc-cn)
    (setq file-name-coding-system 'euc-cn)
    
    ;; 另外建议按下面的先后顺序来设置中文编码识别方式。
    ;; 重要提示:写在最后一行的，实际上最优先使用; 最前面一行，反而放到最后才识别。
    ;; utf-16le-with-signature 相当于 Windows 下的 Unicode 编码，这里也可写成
    ;; utf-16 (utf-16 实际上还细分为 utf-16le, utf-16be, utf-16le-with-signature等多种)
    (prefer-coding-system 'cp950)
    (prefer-coding-system 'gb2312)
    (prefer-coding-system 'cp936)
    (prefer-coding-system 'gb18030)
    
    ;(prefer-coding-system 'utf-16le-with-signature)
    (prefer-coding-system 'utf-16)
    
    ;; 新建文件使用utf-8-unix方式
    ;; 如果不写下面两句，只写
    ;; (prefer-coding-system 'utf-8)
    ;; 这一句的话，新建文件以utf-8编码，行末结束符平台相关
    (prefer-coding-system 'utf-8-dos)
    (prefer-coding-system 'utf-8-unix)
    ;; 编码设置 end