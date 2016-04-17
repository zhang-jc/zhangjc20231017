title: nginx ngx_http_core_module location指令
tags:
  - web server
  - nginx
categories:
  - 开发工具
  - nginx
date: 2016-01-15 21:33:10
---


	语法：location [ = | ~ | ~* | ^~ ] uri { ... }
	     location @name { ... }
	默认：—
	上下文：server, location

为请求 URI（路径）设置配置。

路径匹配在 URI 规范化以后进行。规范化，就是先将 URI 中形如 “%XX” 的编码字符进行解码，再解析 URI 中的相对路径 “.” 和 “..” 部分，另外还可能会[压缩](http://nginx.org/en/docs/http/ngx_http_core_module.html#merge_slashes)相邻的两个或多个斜线成为一个斜线。

可以使用前缀字符串或者正则表达式定义路径。使用正则表达式需要在路径开始添加 “~*” 前缀修饰语 （不区分大小写），或者 “~” 修饰语（区分大小写）。为了匹配请求的 URI 路径，nginx 先检查前缀字符串定义的路径 (前缀路径)。在这些路径中，最长匹配前缀的路径会被选中并记住。然后 nginx 按在配置文件中的出现顺序检查正则表达式路径。第一个匹配的路径找到后检查会停止，并使用相应的配置。如果找不到匹配的正则表达式路径，那么就使用前面被记住的前缀路径的配置。

路径配置块可以嵌套，下面会提到一些例外。

像 Mac OS X 和 Cygwin 这种不区分大小写的操作系统，匹配前缀字符串忽略大小写(0.7.7)。但是，比较仅限于单字节的编码区域(one-byte locale)。

正则表达式中可以包含匹配组(0.7.40)，结果可以被后面的其他指令使用。

如果最大前缀匹配的路径以“^~”开始，那么nginx不再检查正则表达式。

使用“=”前缀可以定义URI和路径的精确匹配。如果找到精确匹配，则终止查找。例如，如果 “/” 请求频繁，定义 “location = /” 将提高这些请求的处理速度，因为查找过程在第一次比较以后即结束。这样的路径明显不可能包含嵌套路径。

>在 0.7.1 到 0.8.41 的版本中，如果一个请求匹配没有“=” 和“^~”修饰符前缀的路径，查找也会终止，且正则表达式也不会检查。

让我们以实力说明上面的情况：

	location = / {
      [ configuration A ]
	}

	location / {
      [ configuration B ]
	}

	location /documents/ {
      [ configuration C ]
	}

	location ^~ /images/ {
      [ configuration D ]
	}

	location ~* \.(gif|jpg|jpeg)$ {
      [ configuration E ]
	}

“/” 请求将匹配配置 A，“/index.html” 请求将匹配配置B，“/documents/document.html” 请求将匹配配置C，“/images/1.gif” 请求将匹配配置D，“/documents/1.jpg” 请求将匹配配置E。

“@” 前缀定义一个命名的路径。这种路径不会用作常规请求的处理，仅用作请求转发。它们不能嵌套，并且不能包含嵌套路径。

如果一个路径用带有斜杠结尾的前缀字符串定义的，并且请求被 [proxy_pass](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)、[fastcgi_pass](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_pass)、[uwsgi_pass](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_pass)、[scgi_pass](http://nginx.org/en/docs/http/ngx_http_scgi_module.html#scgi_pass) 或者 [memcached_pass](http://nginx.org/en/docs/http/ngx_http_memcached_module.html#memcached_pass)中的一个处理，那么特定的处理会执行。在响应 URI 与该字符串相等的请求，但没有结尾的斜杠，一个带有 301 代码的固定转发将返回给追加斜杠的被请求 URI。如果不喜欢这样，可以像下面这样定义精确匹配 URI 的路径：

	location /user/ {
      proxy_pass http://user.example.com;
	}

	location = /user {
      proxy_pass http://login.example.com;
	}
