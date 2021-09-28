---
title: Hadoop HTTPFS部署问题
date: 2021-09-28 17:41:51
tags:
- 大数据
- Hadoop
- HDFS
- HTTPFS
categories:
- 大数据
- Hadoop
---

官网参考地址：[Hadoop HDFS over HTTP - Server Setup](https://hadoop.apache.org/docs/r3.2.2/hadoop-hdfs-httpfs/ServerSetup.html)。

除了官网配置步骤外，还需要注意在httpfs-site.xml中添加以下配置：

```XML
<property>
  <name>httpfs.proxyuser.{proxyuser}.groups</name>
  <value>*</value>
</property>
<property>
  <name>httpfs.proxyuser.{proxyuser}.hosts</name>
  <value>*</value>
</property>
```

> 注意，该配置项跟core-site.xml中的很像，但是开头部分不一样。

这个问题我是通过跟踪Hadoop源代码解决的：

```Java
/hadoop-hdfs-httpfs/src/main/java/org/apache/hadoop/fs/http/server/HttpFSAuthenticationFilter.java

protected Configuration getProxyuserConfiguration(FilterConfig filterConfig) {
  Map<String, String> proxyuserConf = HttpFSServerWebApp.get().getConfig().
      getValByRegex("httpfs\\.proxyuser\\.");
  Configuration conf = new Configuration(false);
  for (Map.Entry<String, String> entry : proxyuserConf.entrySet()) {
    conf.set(entry.getKey().substring("httpfs.".length()), entry.getValue());
  }
  return conf;
}
```