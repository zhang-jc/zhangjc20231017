title: mongoose安装
tags:
  - nodejs
  - mongoose
  - MongoDB
categories:
  - 开发
  - 环境搭建
date: 2015-12-07 09:51:59
---

首先，已经安装了 MongoDB 和 Node.js 。
然后，用 npm 命令安装 Mongoose 。

	$ npm install mongoose

### gyp ERR! build error
如果安装过程中遇到以下错误，请尝试下面的解决方法：

	make: *** [Release/obj.target/kerberos/lib/kerberos.o] Error 1
	make: Leaving directory `/letv/DataInspector/node_modules/mongoose/node_modules/mongodb/node_modules/kerberos/build'
	gyp ERR! build error
	gyp ERR! stack Error: `make` failed with exit code: 2
	gyp ERR! stack     at ChildProcess.onExit (/letv/nodejs/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:270:23)
	gyp ERR! stack     at emitTwo (events.js:87:13)
	gyp ERR! stack     at ChildProcess.emit (events.js:172:7)
	gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:200:12)
	gyp ERR! System Linux 2.6.32-926.504.30.3.letv.el6.x86_64
	gyp ERR! command "/letv/nodejs/bin/node" "/letv/nodejs/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
	gyp ERR! cwd /letv/DataInspector/node_modules/mongoose/node_modules/mongodb/node_modules/kerberos
	gyp ERR! node -v v4.2.2
	gyp ERR! node-gyp -v v3.0.3
	gyp ERR! not ok

（1）检查 python 是否安装以及版本号

	$ python -V
	Python 2.6.6

（2）检查 g++ 版本

这个检查是借鉴网友的做法。（这块儿真心不懂，感谢度娘和goole，还有热血网友^_^）
如果g++低于 4.8 需要升级g++版本。

	$ g++ -v
	Using built-in specs.
	Target: x86_64-redhat-linux
	Configured with: ../configure --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-languages=c,c++,objc,obj-c++,java,fortran,ada --enable-java-awt=gtk --disable-dssi --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-1.5.0.0/jre --enable-libgcj-multifile --enable-java-maintainer-mode --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --disable-libjava-multilib --with-ppl --with-cloog --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux
	Thread model: posix
	gcc version 4.4.7 20120313 (Red Hat 4.4.7-11) (GCC)

升级方法参考：<http://xg2007524.blog.51cto.com/869106/1337465>
如果参考博文中的第二步“下载编译所需依赖库”不能自动完成的话，可以根据 contrib/download_prerequisites 中的内容手动下载，并运行相应的命令完成操作。

完成 gcc 升级后，需要替换系统默认的g++

	$ cd /usr/bin
	$ rm -f g++
	$ ln -s /usr/local/bin/g++ g++
	$ g++ --version
	g++ (GCC) 4.8.2
	Copyright (C) 2013 Free Software Foundation, Inc.
	This is free software; see the source for copying conditions.  There is NO
	warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

g++ 升级完成，重新安装 mongoose 成功。
