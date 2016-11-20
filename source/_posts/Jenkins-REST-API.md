title: Jenkins REST API
tags:
  - Jenkins
categories:
  - 开发工具
  - Jenkins
date: 2015-11-24 13:12:59
---

#### buildWithParameters（参数化构建项目）

	url=http://${ip}:${port}/job/${projectName}/buildWithParameters?args=${argsValue}
	curl -X POST ${url} --user ${userName}:${password}

<!-- more -->

url  --  完整的 REST API 请求地址，包含请求参数  
ip  --  Jenkins Master 主机的ip地址（如果有域名可以使用域名）  
port  --  Jenkins 服务监听端口  
projectName  --  Jenkins 通过参数构建的项目的名称  
args --  要构建项目配置的构建时需要输入参数的名称，在构建任务内部可以通过 ${args} 获取输入的值（以 shell 为例）  
argsValue  --  构建时需要输入的参数值  
curl -X POST  --  以 POST 方法请求  
userName  --  具有构建项目权限的用户名  
password  --  具有构建项目权限的用户的口令

注意：

- 如果参数中包含 url 保留字符或者中文需要做 url 编码，如：

	url=http://${ip}:${port}/job/${projectName}/buildWithParameters?args=a b

url 编码后变为：

	url=http://${ip}:${port}/job/${projectName}/buildWithParameters?args=a%20b

- 如果 Jenkins 设置匿名用户可以触发构建，则 --user 参数可以不用。安全考虑应禁用匿名用户触发构建。

#### Disable/Enable（禁用/启用项目）

	http://${ip}:${port}/job/${projectName}/enable
	http://${ip}:${port}/job/${projectName}/disable

项目禁用后，该项目在构建队列中的任务不会再构建。
