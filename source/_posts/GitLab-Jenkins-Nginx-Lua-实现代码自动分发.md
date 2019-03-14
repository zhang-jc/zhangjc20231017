title: GitLab + Jenkins + Nginx + Lua 实现代码自动分发
date: 2019-03-13 22:48:30
tags:
- GitLab
- Jenkins
- Nginx
- Lua
- OpenResty
categories:
- 开发
- CI
---
在大数据 Hive 数据仓库的开发中，主要是 shell + HQL 的脚本开发。脚本的测试需要放到适当的大数据环境中进行。如果大家共用 GitLab 项目上的同一个 Branch 进行测试需要代码频繁合并，影响效率。我的思路是每个 Developer 在自己的 Branch 开发并经过测试后发起 Merge Request。  
在我的开发场景中 GitLab 在内网中通过端口映射后允许从外网访问，GitLab 中的 IP 都是内网的，GitLab 上的项目链接地址都是内网的，如（ssh://git@192.168.1.10:50022/test/test.git）。并且我的 Jenkins 也是在内网中的，所以需要通过一层代理接收外部的 REST API 请求。这一点给最终的实现带来一些难点。

<!-- more -->

#### 实现架构

![数据仓库开发测试代码分发流程](/uploads/20190314/数据仓库开发测试代码分发流程.png)

#### GitLab 项目配置

在 GitLab 项目上配置 WebHook：![GitLabWebHook](/uploads/20190314/GitLabWebHook.png)

这样每次项目的 push 操作都会向这个配置的 URL 发送一个 Post 请求。每次 WebHook 的执行情况可以点击 WebHook 后面的 Edit 按钮在编辑页面中查看。

#### Nginx + Lua

##### OpenResty 安装

直接使用推荐的预编译 OpenResty 安装。Centos7.3 执行以下命令：

    sudo yum install yum-utils
    sudo yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
    sudo yum install openresty

##### 安装 OpenResty Lua HTTP 客户端模块

下载 GitHub 项目代码：[lua-resty-http](https://github.com/ledgetech/lua-resty-http)

将 lib/resty 目录下的两个文件 http.lua 和 http_headers.lua 放到 OpenResty 安装对应的目录下（如：/usr/local/openresty/lualib/resty）。

##### Nginx 配置

nginx.conf 配置内容如下。主要是使用 Lua 解析从 GitLab WebHook 发送来的数据，并解析需要的参数后通过 Jenkins 的 REST API 发送给 Jenkins。

    server {
        listen       8080;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            content_by_lua_block {
                local cjson = require "cjson"
                local http = require "resty.http"
                local httpc = http.new()

                ngx.req.read_body()
                local data = ngx.req.get_body_data()
                local json = cjson.decode(data)
                local after = json["after"]
                if ( after == "0000000000000000000000000000000000000000" )
                then
                    ngx.status = 200
                    ngx.say("delete branch do not need build")
                    return
                end

                local projectName = json["project"]["name"]
                local userName = json["user_name"]
                local ref = json["ref"]
                local branchName = string.sub(ref, 12)
                if ( branchName == "master" )
                then
                    ngx.status = 200
                    ngx.say("master branch do not need build")
                    return
                end

                local uri = "http://172.16.72.200:8080/job/DataWarehouse/buildWithParameters?userName="
                uri = uri..userName.."&branchName="..branchName.."&projectName="..projectName
                local res, err = httpc:request_uri(uri, {
                    method = "POST"
                })

                ngx.status = res.status
                ngx.say(res.body)
            }
        }
    }

#### Jenkins 项目配置

##### 配置参数化构建的项目

注意参数名称与 Lua 发送请求的参数名称要对应，如下图：![](/uploads/20190314/JenkinsProject1.png)

##### 代发分发逻辑

在构建的 Shell 中实现代码分发到对应 Developer 的个人目录下：![](/uploads/20190314/JenkinsProject2.png)
