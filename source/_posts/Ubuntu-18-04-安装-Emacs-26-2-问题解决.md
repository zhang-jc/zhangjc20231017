title: Ubuntu 18.04 安装 Emacs 26.2 问题解决
date: 2019-06-04 15:16:43
tags:
- Ubuntu
- Emacs
categories:
- 开发工具
- Emacs
---
### no X development libraries were found

    checking for X... no
    checking for X... true
    configure: error: You seem to be running X, but no X development libraries
    were found.  You should install the relevant development files for X
    and for the toolkit you want, such as Gtk+ or Motif.  Also make
    sure you have development files for image handling, i.e.
    tiff, gif, jpeg, png and xpm.
    If you are sure you want Emacs compiled without X window support, pass
      --without-x
    to configure.

安装 libgtk 开发包：

    $ sudo apt-get install libgtk2.0-dev

<!-- more -->

### libXpm libjpeg libgif/libungif libtiff gnutls were not found

    configure: error: The following required libraries were not found:
        libXpm libjpeg libgif/libungif libtiff gnutls
    Maybe some development libraries/packages are missing?
    If you don't want to link with them give
        --with-xpm=no --with-jpeg=no --with-gif=no --with-tiff=no --with-gnutls=no
    as options to configure

安装对应的包：

    sudo apt-get install libxpm-dev
    sudo apt-get install libjpeg62-dev
    sudo apt-get install libgif-dev
    sudo apt-get install libtiff5-dev
    sudo apt-get install libgnutls28-dev
