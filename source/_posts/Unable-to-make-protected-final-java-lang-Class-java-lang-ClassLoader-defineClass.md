---
title: >-
  Unable to make protected final java.lang.Class
  java.lang.ClassLoader.defineClass
date: 2022-08-28 00:31:09
tags:
- Java
- Spring
- Spring Boot
categories:
- 语言
- Java
---

错误信息如下：

```Log
2022-08-28 00:29:37.447 ERROR 123200 --- [           main] o.s.boot.SpringApplication               : Application startup failed

java.lang.IllegalStateException: Cannot load configuration class: org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.enhanceConfigurationClasses(ConfigurationClassPostProcessor.java:403) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.postProcessBeanFactory(ConfigurationClassPostProcessor.java:249) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(PostProcessorRegistrationDelegate.java:281) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(PostProcessorRegistrationDelegate.java:125) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.invokeBeanFactoryPostProcessors(AbstractApplicationContext.java:687) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:525) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.boot.context.embedded.EmbeddedWebApplicationContext.refresh(EmbeddedWebApplicationContext.java:122) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:693) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:360) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:303) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1118) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1107) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at com.frin.controller.HelloController.main(HelloController.java:17) ~[classes/:na]
Caused by: java.lang.ExceptionInInitializerError: null
	at org.springframework.context.annotation.ConfigurationClassEnhancer.newEnhancer(ConfigurationClassEnhancer.java:122) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.annotation.ConfigurationClassEnhancer.enhance(ConfigurationClassEnhancer.java:110) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.enhanceConfigurationClasses(ConfigurationClassPostProcessor.java:393) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	... 12 common frames omitted
Caused by: org.springframework.cglib.core.CodeGenerationException: java.lang.reflect.InaccessibleObjectException-->Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain) throws java.lang.ClassFormatError accessible: module java.base does not "opens java.lang" to unnamed module @7a4ccb53
	at org.springframework.cglib.core.ReflectUtils.defineClass(ReflectUtils.java:464) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.AbstractClassGenerator.generate(AbstractClassGenerator.java:336) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.AbstractClassGenerator$ClassLoaderData$3.apply(AbstractClassGenerator.java:93) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.AbstractClassGenerator$ClassLoaderData$3.apply(AbstractClassGenerator.java:91) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.internal.LoadingCache$2.call(LoadingCache.java:54) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264) ~[na:na]
	at org.springframework.cglib.core.internal.LoadingCache.createEntry(LoadingCache.java:61) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.internal.LoadingCache.get(LoadingCache.java:34) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.AbstractClassGenerator$ClassLoaderData.get(AbstractClassGenerator.java:116) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.AbstractClassGenerator.create(AbstractClassGenerator.java:291) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.KeyFactory$Generator.create(KeyFactory.java:221) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.KeyFactory.create(KeyFactory.java:174) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.KeyFactory.create(KeyFactory.java:153) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.proxy.Enhancer.<clinit>(Enhancer.java:73) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	... 15 common frames omitted
Caused by: java.lang.reflect.InaccessibleObjectException: Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain) throws java.lang.ClassFormatError accessible: module java.base does not "opens java.lang" to unnamed module @7a4ccb53
	at java.base/java.lang.reflect.AccessibleObject.checkCanSetAccessible(AccessibleObject.java:354) ~[na:na]
	at java.base/java.lang.reflect.AccessibleObject.checkCanSetAccessible(AccessibleObject.java:297) ~[na:na]
	at java.base/java.lang.reflect.Method.checkCanSetAccessible(Method.java:199) ~[na:na]
	at java.base/java.lang.reflect.Method.setAccessible(Method.java:193) ~[na:na]
	at org.springframework.cglib.core.ReflectUtils$1.run(ReflectUtils.java:61) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at java.base/java.security.AccessController.doPrivileged(AccessController.java:569) ~[na:na]
	at org.springframework.cglib.core.ReflectUtils.<clinit>(ReflectUtils.java:52) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.KeyFactory$Generator.generateClass(KeyFactory.java:243) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.DefaultGeneratorStrategy.generate(DefaultGeneratorStrategy.java:25) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.cglib.core.AbstractClassGenerator.generate(AbstractClassGenerator.java:329) ~[spring-core-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	... 27 common frames omitted

2022-08-28 00:29:37.448  INFO 123200 --- [           main] ationConfigEmbeddedWebApplicationContext : Closing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@548a24a: startup date [Sun Aug 28 00:29:37 CST 2022]; root of context hierarchy
2022-08-28 00:29:37.449  WARN 123200 --- [           main] ationConfigEmbeddedWebApplicationContext : Exception thrown from LifecycleProcessor on context close

java.lang.IllegalStateException: LifecycleProcessor not initialized - call 'refresh' before invoking lifecycle methods via the context: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@548a24a: startup date [Sun Aug 28 00:29:37 CST 2022]; root of context hierarchy
	at org.springframework.context.support.AbstractApplicationContext.getLifecycleProcessor(AbstractApplicationContext.java:427) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.doClose(AbstractApplicationContext.java:999) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.close(AbstractApplicationContext.java:958) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.boot.SpringApplication.handleRunFailure(SpringApplication.java:750) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:314) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1118) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1107) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at com.frin.controller.HelloController.main(HelloController.java:17) ~[classes/:na]

2022-08-28 00:29:37.449 ERROR 123200 --- [           main] o.s.b.f.s.DefaultListableBeanFactory     : Destroy method on bean with name 'org.springframework.boot.autoconfigure.internalCachingMetadataReaderFactory' threw an exception

java.lang.IllegalStateException: ApplicationEventMulticaster not initialized - call 'refresh' before multicasting events via the context: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@548a24a: startup date [Sun Aug 28 00:29:37 CST 2022]; root of context hierarchy
	at org.springframework.context.support.AbstractApplicationContext.getApplicationEventMulticaster(AbstractApplicationContext.java:414) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.ApplicationListenerDetector.postProcessBeforeDestruction(ApplicationListenerDetector.java:97) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.DisposableBeanAdapter.destroy(DisposableBeanAdapter.java:253) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.destroyBean(DefaultSingletonBeanRegistry.java:578) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.destroySingleton(DefaultSingletonBeanRegistry.java:554) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.destroySingleton(DefaultListableBeanFactory.java:961) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.destroySingletons(DefaultSingletonBeanRegistry.java:523) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.FactoryBeanRegistrySupport.destroySingletons(FactoryBeanRegistrySupport.java:230) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.destroySingletons(DefaultListableBeanFactory.java:968) ~[spring-beans-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.destroyBeans(AbstractApplicationContext.java:1030) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.doClose(AbstractApplicationContext.java:1006) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.context.support.AbstractApplicationContext.close(AbstractApplicationContext.java:958) ~[spring-context-4.3.11.RELEASE.jar:4.3.11.RELEASE]
	at org.springframework.boot.SpringApplication.handleRunFailure(SpringApplication.java:750) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:314) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1118) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1107) ~[spring-boot-1.5.7.RELEASE.jar:1.5.7.RELEASE]
	at com.frin.controller.HelloController.main(HelloController.java:17) ~[classes/:na]
```

这是由于 JDK 8 中有关反射相关的功能自从 JDK 9 开始就被限制了，两种方法解决：

- 为了兼容之前的版本，在运行项目时添加以下参数：

```Java
--add-opens java.base/java.lang=ALL-UNNAMED
```

- 升级至 JDK 9 以上版本。Maven 项目可以在 pom.xml 中添加以下配置：

```XML
<properties>
    <java.version>11</java.version>
</properties>
```