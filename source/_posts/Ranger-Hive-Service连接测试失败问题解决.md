title: Ranger Hive Service连接测试失败问题解决
date: 2020-12-02 10:56:59
tags:
- Ranger
- Hive
- 大数据
categories:
- 大数据
- Ranger
---

异常信息如下：

	org.apache.ranger.plugin.client.HadoopException: Unable to connect to Hive Thrift Server instance..
	Unable to connect to Hive Thrift Server instance..
	Could not open client transport with JDBC Uri: jdbc:hive2://192.168.72.212:10000: Could not establish connection to jdbc:hive2://192.168.72.212:10000: Required field 'client_protocol' is unset! Struct:TOpenSessionReq(client_protocol:null, configuration:{set:hiveconf:hive.server2.thrift.resultset.default.fetch.size=1000, use:database=default}).
	Could not establish connection to jdbc:hive2://192.168.72.212:10000: Required field 'client_protocol' is unset! Struct:TOpenSessionReq(client_protocol:null, configuration:{set:hiveconf:hive.server2.thrift.resultset.default.fetch.size=1000, use:database=default}).
	Required field 'client_protocol' is unset! Struct:TOpenSessionReq(client_protocol:null, configuration:{set:hiveconf:hive.server2.thrift.resultset.default.fetch.size=1000, use:database=default}). 

百度后是因为Hive版本不匹配导致的。我的测试环境Hive版本是2.1.1，通过下面的命令发现确实版本不一致：

	# find . -name "*hive*"
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive/ranger-hive-plugin-1.2.0.jar
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive/hive-common-2.3.2.jar
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive/hive-service-2.3.2.jar
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive/hive-exec-2.3.2.jar
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive/hive-metastore-2.3.2.jar
	./ews/webapp/WEB-INF/classes/ranger-plugins/hive/hive-jdbc-2.3.2.jar
	
问题解决方法就显而易见了。