title: Node JS 安装
tags: nodejs
categories:
  - 开发
  - 环境搭建
date: 2015-11-25 16:45:45
---

### 系统环境
	[root@vm-10-176-30-167 ~]# cat /etc/redhat-release
	CentOS release 6.6
### 下载 nodejs
下载地址：<https://nodejs.org/en/download/>  
我下载的是**Linux Binaries (.tar.gz) 64-bit**
### 解压
	[root@vm-10-176-30-167 letv]# tar xzvf node-v4.2.2-linux-x64.tar.gz
	[root@vm-10-176-30-167 letv]# mv node-v4.2.2-linux-x64 nodejs
为了缩短目录名字，将解压后的目录重命名为 nodejs。
### 配置 path
	[root@vm-10-176-30-167 letv]# vi /etc/profile
添加以下内容，并保存退出。
	PATH=${PATH}:/root/nodejs/bin
	export PATH
### 测试 nodejs
	[root@vm-10-176-30-167 letv]# node -v
	v4.2.2
	[root@vm-10-176-30-167 letv]# npm -v
	2.14.7
此时，node 和 npm 安装完成。
### 安装 Express
创建一个目录，然后进入此目录并将其作为当前工作目录。

	[root@vm-10-176-30-167 letv]# mkdir DataInspector
	[root@vm-10-176-30-167 letv]# cd DataInspector/
通过 npm init 命令为应用创建一个 package.json 文件。

	[root@vm-10-176-30-167 DataInspector]# npm init
此命令将要求你输入几个参数，例如此应用的名称和版本。 应用名称不能包含大写字母，如果默认应用名称包含大写字母会提示错误，并要求重新录入：

	name: (DataInspector) 
	Sorry, name can no longer contain capital letters.
	name: (DataInspector) data_inspector

参数可以直接按“回车”键接受默认设置即可，下面这个除外：

	entry point: (index.js)
键入 app.js 或者你所希望的名称，这是当前应用的入口文件。如果你希望采用默认的 index.js 文件名，只需按“回车”键即可。此处，我将应用入库文件名改为 app.js：

	entry point: (index.js) app.js

接下来安装 Express 并将其保存到依赖列表中：

	[root@vm-10-176-30-167 DataInspector]# npm install express --save
如果只是临时安装 Express，不想将它添加到依赖列表中，只需略去 --save 参数即可：

	[root@vm-10-176-30-167 DataInspector]# npm install express

>安装 Node 模块时，如果指定了 --save 参数，那么此模块将被添加到 package.json 文件中 dependencies 依赖列表中。 然后通过 npm install 命令即可自动安装依赖列表中所列出的所有模块。

### Express Hello World
创建 app.js 文件：

	[root@vm-10-176-30-167 DataInspector]# vi app.js
复制以下代码到 app.js：

	var express = require('express');
	var app = express();

	app.get('/', function (req, res) {
		res.send('Hello World!');
	});

	var server = app.listen(3000, function () {
	  	var host = server.address().address;
	  	var port = server.address().port;
	
	  	console.log('Example app listening at http://%s:%s', host, port);
	});
上面的代码启动一个服务并监听从 3000 端口进入的所有连接请求。他将对所有 (/) URL 或 ***路由*** 返回 “Hello World!” 字符串。对于其他所有路径全部返回 **404 Not Found**。
>req (请求) 和 res (响应) 与 Node 提供的对象完全一致，因此，你可以调用 req.pipe()、req.on('data', callback) 以及任何 Node 提供的方法。

通过如下命令启动此应用：

	[root@vm-10-176-30-167 DataInspector]# node app.js
	Example app listening at http://:::3000
然后在浏览器中打开 <http://10.176.30.167:3000/> 并查看输出结果。如果是在个人电脑上，在浏览器中打开 <http://localhost:3000/> 并查看数据结果。如果看到以下输出结果，则安装成功：

![express hello world](http://www.zhangjc.com/uploads/20151125/nodejs1.png)