title: spacemacs gnuplot
tags:
  - Emacs
categories:
  - 开发工具
  - Emacs
date: 2017-02-27 18:14:46
---

### 环境

- Ubuntu 16.10
- Emacs 24

### 安装过程

#### spacemacs 安装

1. 安装 Emacs

    sudo apt-get install emacs

<!-- more -->

2. 安装 spacemacs

（1）如果已经存在 Emacs 配置文件，首先备份：

    cd ~
    mv .emacs.d .emacs.d.bak
    mv .emacs .emacs.bak

不要忘记备份并移动 ~/.emacs 文件，否则 Spacemacs 将不能加载，因为这个文件阻止从适当的初始化文件加载。

（2）clone 配置仓库：

    git clone https://github.com/syl20bnr/spacemacs ~/.emacs.d

master 分支是稳定不能修改的分支，不要做任何修改，否则会破坏更新机制。使用 develop 分支可以安全的手动处理更新。

（3）【可选】安装 [Source Code Pro](https://github.com/adobe-fonts/source-code-pro) 字体：

即使在终端运行也需要改变终端字体设置。

（4）启动 Emacs。Spacemacs 会自动安装它需要的包。如果看到跟包下载相关的错误，那么可以尝试通过以下命令启动 emacs 禁用 HTTPS 协议：

    emacs --insecure

或者可以设置隐藏文件中的 dotspacemacs-elpa-https 为 nil，这样启动 emacs 的时候就不需要 --insecure 参数了。可以清理 .emacs.d/elpa 目录，这样任何已经下载的可能引起错误的包会重新安装。

（5）重新启动 emacs 完成安装。

#### spacemacs 安装镜像

如果不使用镜像 spacemacs 安装会很慢，甚至卡死。我使用的是 [ELPA 镜像](http://www.4gamers.cn/)。

#### 安装 gnuplot

    apt-get install gnuplot

### gnuplot 测试

编辑 org 文件：

    #+PLOT: title:"Citas" ind:1 deps:(3) type:2d with:histograms set:"yrange [0:]"
         | Sede      | Max cites | H-index |
         |-----------+-----------+---------|
         | Chile     |    257.72 |   21.39 |
         | Leeds     |    165.77 |   19.68 |
         | Sao Paolo |     71.00 |   11.50 |
         | Stockholm |    134.19 |   14.33 |
         | Morelia   |    257.56 |   17.67 |

运行命令 M-x org-plot/gnuplot

> 如果命令运行报错：cannot open load file no such file or directory, gnuplot。原因是没有安装 gnuplot.el。运行命令 M-x package-install RET gnuplot RET
> 参考网址：<https://github.com/bruceravel/gnuplot-mode>