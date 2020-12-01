title: Ranger Admin安装MySQL初始化问题解决
date: 2020-12-01 16:47:49
tags:
- 大数据
- Ranger
categories:
- 大数据
- Ranger
---

告警信息及如何解决见我的另外一篇博客：http://zhang-jc.github.io/2020/11/27/MySQL-JDBC-%E8%BF%9E%E6%8E%A5%E5%BC%82%E5%B8%B8%EF%BC%9Ajavax-net-ssl-SSLException-closing-inbound-before-receiving-peer-s-close-notify/

但是Ranger的安装配置install.properties中未启用ssl，配置信息如下：

	#SSL config
	db_ssl_enabled=false
	db_ssl_required=false
	db_ssl_verifyServerCertificate=false
	#db_ssl_auth_type=1-way|2-way, where 1-way represents standard one way ssl authentication and 2-way represents mutual ssl authentication
	db_ssl_auth_type=2-way
	javax_net_ssl_keyStore=
	javax_net_ssl_keyStorePassword=
	javax_net_ssl_trustStore=
	javax_net_ssl_trustStorePassword=
	
跟踪源代码发现安装过程只处理了启用ssl的情况，但是新版本的mysql默认是需要启用ssl的。修改Ranger安装源代码（apache-ranger-1.2.0/security-admin/scripts/db_setup.py）增加下面代码中的221、222行，如下：

    210         def get_jisql_cmd(self, user, password ,db_name):
    211                 path = RANGER_ADMIN_HOME
    212                 db_ssl_param=''
    213                 db_ssl_cert_param=''
    214                 if self.db_ssl_enabled == 'true':
    215                         db_ssl_param="?useSSL=%s&requireSSL=%s&verifyServerCertificate=%s" %(self.db_ssl_enabled,self.db_ssl_required,self.db_ssl_verifyServerCertificate)
    216                         if self.db_ssl_verifyServerCertificate == 'true':
    217                                 if self.db_ssl_auth_type == '1-way':
    218                                         db_ssl_cert_param=" -Djavax.net.ssl.trustStore=%s -Djavax.net.ssl.trustStorePassword=%s " %(self.javax_net_ssl_trustStore,self.javax_net_ssl_trustStorePassword)
    219                                 else:
    220                                         db_ssl_cert_param=" -Djavax.net.ssl.keyStore=%s -Djavax.net.ssl.keyStorePassword=%s -Djavax.net.ssl.trustStore=%s -Djavax.net.ssl.trustStorePassword=%s " %(self.javax_net_ssl_keyStore,self.javax_net_ssl_keyStorePassword,self.javax_net_ssl_trustStore,self.javax_net_ssl_trustStorePassword)
    221                 else:
    222                         db_ssl_param='?useSSL=false'