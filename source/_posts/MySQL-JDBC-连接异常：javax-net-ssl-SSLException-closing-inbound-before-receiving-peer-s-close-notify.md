title: >-
  MySQL JDBC 连接异常：javax.net.ssl.SSLException: closing inbound before receiving
  peer's close_notify
date: 2020-11-27 09:40:17
tags:
- MySQL
categories:
- 数据库
- MySQL
---

异常信息如下：

	Thu Nov 26 23:54:22 CST 2020 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
	Thu Nov 26 23:54:23 CST 2020 WARN: Caught while disconnecting...
	
	EXCEPTION STACK TRACE:
	
	
	
	** BEGIN NESTED EXCEPTION ** 
	
	javax.net.ssl.SSLException
	MESSAGE: closing inbound before receiving peer's close_notify
	
	STACKTRACE:
	
	javax.net.ssl.SSLException: closing inbound before receiving peer's close_notify
		at sun.security.ssl.Alert.createSSLException(Alert.java:133)
		at sun.security.ssl.Alert.createSSLException(Alert.java:117)
		at sun.security.ssl.TransportContext.fatal(TransportContext.java:314)
		at sun.security.ssl.TransportContext.fatal(TransportContext.java:270)
		at sun.security.ssl.TransportContext.fatal(TransportContext.java:261)
		at sun.security.ssl.SSLSocketImpl.shutdownInput(SSLSocketImpl.java:656)
		at sun.security.ssl.SSLSocketImpl.shutdownInput(SSLSocketImpl.java:635)
		at com.mysql.jdbc.MysqlIO.quit(MysqlIO.java:2246)
		at com.mysql.jdbc.ConnectionImpl.realClose(ConnectionImpl.java:4201)
		at com.mysql.jdbc.ConnectionImpl.close(ConnectionImpl.java:1472)
		at com.jolbox.bonecp.BoneCP.obtainRawInternalConnection(BoneCP.java:353)
		at com.jolbox.bonecp.BoneCP.<init>(BoneCP.java:416)
		at com.jolbox.bonecp.BoneCPDataSource.getConnection(BoneCPDataSource.java:120)
		at org.datanucleus.store.rdbms.ConnectionFactoryImpl$ManagedConnectionImpl.getConnection(ConnectionFactoryImpl.java:483)
		at org.datanucleus.store.rdbms.RDBMSStoreManager.<init>(RDBMSStoreManager.java:296)
		at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
		at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
		at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
		at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
		at org.datanucleus.plugin.NonManagedPluginRegistry.createExecutableExtension(NonManagedPluginRegistry.java:606)
		at org.datanucleus.plugin.PluginManager.createExecutableExtension(PluginManager.java:301)
		at org.datanucleus.NucleusContextHelper.createStoreManagerForProperties(NucleusContextHelper.java:133)
		at org.datanucleus.PersistenceNucleusContextImpl.initialise(PersistenceNucleusContextImpl.java:420)
		at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.freezeConfiguration(JDOPersistenceManagerFactory.java:821)
		at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.createPersistenceManagerFactory(JDOPersistenceManagerFactory.java:338)
		at org.datanucleus.api.jdo.JDOPersistenceManagerFactory.getPersistenceManagerFactory(JDOPersistenceManagerFactory.java:217)
		at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
		at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
		at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
		at java.lang.reflect.Method.invoke(Method.java:498)
		at javax.jdo.JDOHelper$16.run(JDOHelper.java:1965)
		at java.security.AccessController.doPrivileged(Native Method)
		at javax.jdo.JDOHelper.invoke(JDOHelper.java:1960)
		at javax.jdo.JDOHelper.invokeGetPersistenceManagerFactoryOnImplementation(JDOHelper.java:1166)
		at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:808)
		at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:701)
		at org.apache.hadoop.hive.metastore.ObjectStore.getPMF(ObjectStore.java:515)
		at org.apache.hadoop.hive.metastore.ObjectStore.getPersistenceManager(ObjectStore.java:544)
		at org.apache.hadoop.hive.metastore.ObjectStore.initializeHelper(ObjectStore.java:399)
		at org.apache.hadoop.hive.metastore.ObjectStore.initialize(ObjectStore.java:336)
		at org.apache.hadoop.hive.metastore.ObjectStore.setConf(ObjectStore.java:297)
		at org.apache.hadoop.util.ReflectionUtils.setConf(ReflectionUtils.java:76)
		at org.apache.hadoop.util.ReflectionUtils.newInstance(ReflectionUtils.java:136)
		at org.apache.hadoop.hive.metastore.RawStoreProxy.<init>(RawStoreProxy.java:58)
		at org.apache.hadoop.hive.metastore.RawStoreProxy.getProxy(RawStoreProxy.java:67)
		at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.newRawStore(HiveMetaStore.java:599)
		at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.getMS(HiveMetaStore.java:564)
		at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.createDefaultDB(HiveMetaStore.java:626)
		at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.init(HiveMetaStore.java:416)
		at org.apache.hadoop.hive.metastore.RetryingHMSHandler.<init>(RetryingHMSHandler.java:78)
		at org.apache.hadoop.hive.metastore.RetryingHMSHandler.getProxy(RetryingHMSHandler.java:84)
		at org.apache.hadoop.hive.metastore.HiveMetaStore.newRetryingHMSHandler(HiveMetaStore.java:6490)
		at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.<init>(HiveMetaStoreClient.java:238)
		at org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient.<init>(SessionHiveMetaStoreClient.java:70)
		at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
		at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
		at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
		at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
		at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1652)
		at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.<init>(RetryingMetaStoreClient.java:80)
		at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.getProxy(RetryingMetaStoreClient.java:130)
		at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.getProxy(RetryingMetaStoreClient.java:101)
		at org.apache.hadoop.hive.ql.metadata.Hive.createMetaStoreClient(Hive.java:3367)
		at org.apache.hadoop.hive.ql.metadata.Hive.getMSC(Hive.java:3406)
		at org.apache.hadoop.hive.ql.metadata.Hive.getMSC(Hive.java:3386)
		at org.apache.hadoop.hive.ql.metadata.Hive.getAllFunctions(Hive.java:3640)
		at org.apache.hadoop.hive.ql.metadata.Hive.reloadFunctions(Hive.java:236)
		at org.apache.hadoop.hive.ql.metadata.Hive.registerAllFunctionsOnce(Hive.java:221)
		at org.apache.hadoop.hive.ql.metadata.Hive.<init>(Hive.java:366)
		at org.apache.hadoop.hive.ql.metadata.Hive.create(Hive.java:310)
		at org.apache.hadoop.hive.ql.metadata.Hive.getInternal(Hive.java:290)
		at org.apache.hadoop.hive.ql.metadata.Hive.get(Hive.java:266)
		at org.apache.hadoop.hive.ql.session.SessionState.start(SessionState.java:558)
		at org.apache.hadoop.hive.ql.session.SessionState.beginStart(SessionState.java:531)
		at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:705)
		at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:641)
		at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
		at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
		at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
		at java.lang.reflect.Method.invoke(Method.java:498)
		at org.apache.hadoop.util.RunJar.run(RunJar.java:221)
		at org.apache.hadoop.util.RunJar.main(RunJar.java:136)
	
	
	** END NESTED EXCEPTION **

解决方法：在 JDBC 连接串中添加 useSSL=false 配置，如：jdbc:mysql://192.168.72.212/hive?createDatabaseIfNotExist=true&amp;characterEncoding=utf-8&amp;useSSL=false

注意，XML 中的 \& 需要改写为：\&amp;

