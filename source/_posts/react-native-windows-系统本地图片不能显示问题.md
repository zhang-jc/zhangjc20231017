title: react native windows 系统本地图片不能显示问题
tags: react-native
categories:
  - 语言
  - react native
date: 2015-12-20 11:53:57
---


在 windows 开发环境下，使用 react native 的 Image UI 组件通过相对路径加载本地图片显示不出来，可以通过修改 react native 源代码解决。但是，通过这种方式不确定是否会引起其他平台下新的问题。

将文件 packager/react-packager/src/Bundler/index.js 中的下面一行：

	httpServerLocation: path.join('/assets', path.dirname(relPath)),

修改为：

	httpServerLocation: path.join('assets', path.dirname(relPath)),
