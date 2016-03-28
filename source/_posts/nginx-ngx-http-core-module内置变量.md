title: nginx ngx_http_core_module 内置变量
tags:
  - web server
  - nginx
categories:
  - 开发工具
  - nginx
date: 2016-01-01 16:14:31
---

ngx_http_core_module 模块支持与 Apache 服务器相同名称的内置变量。首先，这些都是表示客户端请求头字段的变量，例如 $http_user_agent 、$http_cookie 等等。还有其他变量：

$arg_name

请求行中的参数名称

$args

请求行中的参数

$binary_remote_addr

二进制形式的客户端地址，值的长度总是 4 个字节

$body_bytes_sent

发送给客户端的字节数，不算响应头；这个变量兼容 mod_log_config Apache 模块的“%B”参数。

$bytes_sent

发送给客户端的字节数(1.3.8, 1.2.5)

$connection

连接序列数(1.3.8, 1.2.5)

$connection_requests

一个连接产生的当前请求数 (1.3.8, 1.2.5)

$content_length

请求头变量“Content-Length”

$content_type

请求头变量“Content-Type”

$cookie_name

cookie 名称

$document_root

当前请求的 root 或 alias 指令的值

$document_uri

同 $uri

$host

按照这个优先级顺序：请求行的主机名，请求头参数“Host” 中的主机名，匹配请求的服务器名称

$hostname

主机名称

$http_*name*

任意请求头参数；变量名的最后部分是转成小写的参数名，并且用下划线替换破折号。

$https

“on”如果连接运行在 SSL 模式，否则是一个空字符串。

$is_args

“?”如果请求行有参数，否则是一个空字符串。

$limit_rate

设置这个变量启用响应频率限制；参见 [limit_rate](http://nginx.org/en/docs/http/ngx_http_core_module.html#limit_rate)

$msec

以毫秒为单位的当前时间的秒数 (1.3.9, 1.2.6)

$nginx_version

nginx 版本

$pid

工作进程的 PID

$pipe

如果请求来自管道通信，值为“p”，否则为“.” (1.3.12, 1.2.7)

$proxy_protocol_addr

获取代理代理协议头的客户端地址，如果是直接访问，该值为空字符串。(1.5.12)  
代理协议必须预先在 [listen](http://nginx.org/en/docs/http/ngx_http_core_module.html#listen) 指令中通过设置 proxy_protocol 参数启用。

$query_string

同 $args

$realpath_root

当前请求的文档根目录或别名的真实路径，会将所有符号连接转换为真实路径。

$remote_addr

客户端地址

$remote_port

客户端端口

$remote_user

用于HTTP基础认证服务的用户名

$request

完整的原始请求行

$request_body

请求体  
此变量可在 location 中使用，将请求主体通过 proxy_pass, fastcgi_pass, uwsgi_pass, 和 scgi_pass 传递给下一级的代理服务器。

$request_body_file

将客户端请求主体保存在临时文件中。处理过程接收后，需要删除这个文件。如果一直将请求体写入一个文件， [client_body_in_file_only](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_in_file_only) 需要被启用。如果一个临时文件的名称是在代理请求中传递的，或者在请求中传递给一个 FastCGI/uwsgi/SCGI 服务器， 传递请求体需要分别禁用 [proxy_pass_request_body off](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass_request_body) 、 [fastcgi_pass_request_body off](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_pass_request_body) 、 [uwsgi_pass_request_body off](http://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_pass_request_body) 或者 [scgi_pass_request_body off](http://nginx.org/en/docs/http/ngx_http_scgi_module.html#scgi_pass_request_body) 指令。

$request_completion

如果请求完成，则值为“OK”，否则为空字符串。

$request_filename

当前请求的文件路径，基于 [root](http://nginx.org/en/docs/http/ngx_http_core_module.html#root) 或者 [alias](http://nginx.org/en/docs/http/ngx_http_core_module.html#alias) 指令，以及 URI。

$request_length

请求长度（包含请求行、头和请求体）(1.3.12, 1.2.7)

$request_method

请求方法，通常是 “GET” 或者 “POST”

$request_time

请求处理时间秒数，精度到毫秒(1.3.9, 1.2.6)；从客户端第一个字节被读取开始。

$request_uri

完整的请求 URI （带参数）

$scheme

请求方案，“http” 或 “https”

$sent_http\_name

自定义影响头字段；变量名的最后部分是破折号替换为下划线并且转换为小写字符的字段名字。

$server_addr

接受请求的服务器地址  
计算这个变量的值通常需要一个系统调用。为了避免系统调用，[listen](http://nginx.org/en/docs/http/ngx_http_core_module.html#listen)指令必须指定地址，并且使用绑定参数。

$server_name

接受请求的服务器名称

$server_port

接受请求的服务器端口

$server_protocol

请求协议，通常是 “HTTP/1.0” 、 “HTTP/1.1” 或者 “[HTTP/2.0](http://nginx.org/en/docs/http/ngx_http_v2_module.html)”

$status

响应状态(1.3.2, 1.2.2)

$tcpinfo_rtt, $tcpinfo_rttvar, $tcpinfo_snd_cwnd, $tcpinfo_rcv_space

关于客户端 TCP 连接的信息；在支持 TCP_INFO 套接字的系统上可用。

$time_iso8601

ISO 8601 标准格式的本地时间(1.3.12, 1.2.7)

$time_local

Common Log 格式的本地时间(1.3.12, 1.2.7)

$uri

当前请求[标准化](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)的 URI  
$uri 的值在请求处理过程中可能改变，例如，当内部转发，或者使用索引文件时。