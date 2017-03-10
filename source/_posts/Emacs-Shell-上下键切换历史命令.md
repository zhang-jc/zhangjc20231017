title: Emacs Shell 上下键切换历史命令
tags:
  - Emacs
categories:
  - 开发工具
  - Emacs
date: 2017-03-09 23:51:27
---

Emacs 开启 Shell 模式，UP 键默认是上一行，而不是上一条执行过的命令。为了方便，通过以下方式实现 UP 键和 DOWN 键切换历史命令。

<!-- more -->

- 设置 UP 键

    M+x global-set-key
    按 UP 键
    comint-previous-input

- 设置 DOWN 键

    M+x global-set-key
    按 DOWN 键
    comint-nex-input

