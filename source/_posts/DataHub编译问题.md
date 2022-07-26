---
title: DataHub编译问题
date: 2022-07-20 09:27:45
tags:
- Datahub
- 元数据
- 数据治理
- 数据目录
---

# 环境信息

OS：Linux frin 5.15.0-41-generic #44~20.04.1-Ubuntu SMP Fri Jun 24 13:27:29 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

# 问题列表
## sasl/sasl.h: 没有那个文件或目录

```
# apt get install libsasl2-dev libsasl2-modules
```

## lber.h: 没有那个文件或目录

···
sudo apt-get install python-dev libldap2-dev libssl-dev
···

## Docker异常

```
Gradle suite > Gradle test > com.linkedin.metadata.systemmetadata.ElasticSearchSystemMetadataServiceTest > setup FAILED
    org.testcontainers.containers.ContainerFetchException: Can't get Docker image: RemoteDockerImage(imageName=docker.elastic.co/elasticsearch/elasticsearch:7.9.3, imagePullPolicy=DefaultPullPolicy())
        at org.testcontainers.containers.GenericContainer.getDockerImageName(GenericContainer.java:1286)
        at org.testcontainers.containers.GenericContainer.logger(GenericContainer.java:615)
        at org.testcontainers.elasticsearch.ElasticsearchContainer.<init>(ElasticsearchContainer.java:73)
        at com.linkedin.metadata.ElasticTestUtils.getNewElasticsearchContainer(ElasticTestUtils.java:31)
        at com.linkedin.metadata.systemmetadata.ElasticSearchSystemMetadataServiceTest.setup(ElasticSearchSystemMetadataServiceTest.java:37)

        Caused by:
        java.lang.IllegalStateException: Could not find a valid Docker environment. Please see logs and check configuration
            at org.testcontainers.dockerclient.DockerClientProviderStrategy.lambda$getFirstValidStrategy$7(DockerClientProviderStrategy.java:215)
            at java.util.Optional.orElseThrow(Optional.java:290)
            at org.testcontainers.dockerclient.DockerClientProviderStrategy.getFirstValidStrategy(DockerClientProviderStrategy.java:207)
            at org.testcontainers.DockerClientFactory.getOrInitializeStrategy(DockerClientFactory.java:136)
            at org.testcontainers.DockerClientFactory.client(DockerClientFactory.java:178)
            at org.testcontainers.LazyDockerClient.getDockerClient(LazyDockerClient.java:14)
            at org.testcontainers.LazyDockerClient.listImagesCmd(LazyDockerClient.java:12)
            at org.testcontainers.images.LocalImagesCache.maybeInitCache(LocalImagesCache.java:68)
            at org.testcontainers.images.LocalImagesCache.get(LocalImagesCache.java:32)
            at org.testcontainers.images.AbstractImagePullPolicy.shouldPull(AbstractImagePullPolicy.java:18)
            at org.testcontainers.images.RemoteDockerImage.resolve(RemoteDockerImage.java:66)
            at org.testcontainers.images.RemoteDockerImage.resolve(RemoteDockerImage.java:27)
            at org.testcontainers.utility.LazyFuture.getResolvedValue(LazyFuture.java:17)
            at org.testcontainers.utility.LazyFuture.get(LazyFuture.java:39)
            at org.testcontainers.containers.GenericContainer.getDockerImageName(GenericContainer.java:1284)
            ... 4 more

Gradle suite > Gradle test > com.linkedin.metadata.search.LineageSearchServiceTest > setup FAILED
    org.testcontainers.containers.ContainerFetchException: Can't get Docker image: RemoteDockerImage(imageName=docker.elastic.co/elasticsearch/elasticsearch:7.9.3, imagePullPolicy=DefaultPullPolicy())
        at org.testcontainers.containers.GenericContainer.getDockerImageName(GenericContainer.java:1286)
        at org.testcontainers.containers.GenericContainer.logger(GenericContainer.java:615)
        at org.testcontainers.elasticsearch.ElasticsearchContainer.<init>(ElasticsearchContainer.java:73)
        at com.linkedin.metadata.ElasticTestUtils.getNewElasticsearchContainer(ElasticTestUtils.java:31)
        at com.linkedin.metadata.search.LineageSearchServiceTest.setup(LineageSearchServiceTest.java:77)

        Caused by:
        java.lang.IllegalStateException: Previous attempts to find a Docker environment failed. Will not retry. Please see logs and check configuration
            at org.testcontainers.dockerclient.DockerClientProviderStrategy.getFirstValidStrategy(DockerClientProviderStrategy.java:109)
            at org.testcontainers.DockerClientFactory.getOrInitializeStrategy(DockerClientFactory.java:136)
            at org.testcontainers.DockerClientFactory.client(DockerClientFactory.java:178)
            at org.testcontainers.LazyDockerClient.getDockerClient(LazyDockerClient.java:14)
            at org.testcontainers.LazyDockerClient.inspectImageCmd(LazyDockerClient.java:12)
            at org.testcontainers.images.LocalImagesCache.refreshCache(LocalImagesCache.java:42)
            at org.testcontainers.images.AbstractImagePullPolicy.shouldPull(AbstractImagePullPolicy.java:24)
            at org.testcontainers.images.RemoteDockerImage.resolve(RemoteDockerImage.java:66)
            at org.testcontainers.images.RemoteDockerImage.resolve(RemoteDockerImage.java:27)
            at org.testcontainers.utility.LazyFuture.getResolvedValue(LazyFuture.java:17)
            at org.testcontainers.utility.LazyFuture.get(LazyFuture.java:39)
            at org.testcontainers.containers.GenericContainer.getDockerImageName(GenericContainer.java:1284)
            ... 4 more

Gradle suite > Gradle test > com.linkedin.metadata.search.SearchServiceTest > setup FAILED
    org.testcontainers.containers.ContainerFetchException: Can't get Docker image: RemoteDockerImage(imageName=docker.elastic.co/elasticsearch/elasticsearch:7.9.3, imagePullPolicy=DefaultPullPolicy())
        at org.testcontainers.containers.GenericContainer.getDockerImageName(GenericContainer.java:1286)
        at org.testcontainers.containers.GenericContainer.logger(GenericContainer.java:615)
        at org.testcontainers.elasticsearch.ElasticsearchContainer.<init>(ElasticsearchContainer.java:73)
        at com.linkedin.metadata.ElasticTestUtils.getNewElasticsearchContainer(ElasticTestUtils.java:31)
        at com.linkedin.metadata.search.SearchServiceTest.setup(SearchServiceTest.java:56)

        Caused by:
        java.lang.IllegalStateException: Previous attempts to find a Docker environment failed. Will not retry. Please see logs and check configuration
            at org.testcontainers.dockerclient.DockerClientProviderStrategy.getFirstValidStrategy(DockerClientProviderStrategy.java:109)
            at org.testcontainers.DockerClientFactory.getOrInitializeStrategy(DockerClientFactory.java:136)
            at org.testcontainers.DockerClientFactory.client(DockerClientFactory.java:178)
            at org.testcontainers.LazyDockerClient.getDockerClient(LazyDockerClient.java:14)
            at org.testcontainers.LazyDockerClient.inspectImageCmd(LazyDockerClient.java:12)
            at org.testcontainers.images.LocalImagesCache.refreshCache(LocalImagesCache.java:42)
            at org.testcontainers.images.AbstractImagePullPolicy.shouldPull(AbstractImagePullPolicy.java:24)
            at org.testcontainers.images.RemoteDockerImage.resolve(RemoteDockerImage.java:66)
            at org.testcontainers.images.RemoteDockerImage.resolve(RemoteDockerImage.java:27)
            at org.testcontainers.utility.LazyFuture.getResolvedValue(LazyFuture.java:17)
            at org.testcontainers.utility.LazyFuture.get(LazyFuture.java:39)
            at org.testcontainers.containers.GenericContainer.getDockerImageName(GenericContainer.java:1284)
            ... 4 more

Gradle suite > Gradle test > com.linkedin.metadata.graph.dgraph.DgraphGraphServiceTest > setup FAILED
    java.lang.IllegalStateException: Could not find a valid Docker environment. Please see logs and check configuration
        at org.testcontainers.dockerclient.DockerClientProviderStrategy.lambda$getFirstValidStrategy$7(DockerClientProviderStrategy.java:215)
        at java.util.Optional.orElseThrow(Optional.java:290)
        at org.testcontainers.dockerclient.DockerClientProviderStrategy.getFirstValidStrategy(DockerClientProviderStrategy.java:207)
        at org.testcontainers.DockerClientFactory.getOrInitializeStrategy(DockerClientFactory.java:136)
        at org.testcontainers.DockerClientFactory.client(DockerClientFactory.java:178)
        at org.testcontainers.LazyDockerClient.getDockerClient(LazyDockerClient.java:14)
        at org.testcontainers.LazyDockerClient.infoCmd(LazyDockerClient.java:12)
        at com.linkedin.metadata.DockerTestUtils.checkContainerEngine(DockerTestUtils.java:10)
        at com.linkedin.metadata.graph.dgraph.DgraphGraphServiceTest.setup(DgraphGraphServiceTest.java:61)
```

需要注意两点：

- 确保编译用户有Docker操作权限。非root用户运行Docker参见：[Ubuntu非root用户运行Docker](https://www.zhangjc.com/2022/07/21/Ubuntu%E9%9D%9Eroot%E7%94%A8%E6%88%B7%E8%BF%90%E8%A1%8CDocker/)
- 最好停掉现有的Docker容器，避免冲突。

## generateJsonSchema.sh

···Log
Execution failed for task ':docs-website:generateJsonSchema'.
> Process 'command './generateJsonSchema.sh'' finished with non-zero exit value 1
```

编译需要Python3，如果本地有多个Python版本，则可以修改“docs-website/genJsonSchema/gen_json_schema.py”中以下内容：

```Shell
python gen_json_schema.py ../../docs/generated/ingestion/config_schemas/ ${SCHEMA_ROOT_DIR}/${SCHEMA_FILE_NAME}
```

修改为：

```Shell
python3 gen_json_schema.py ../../docs/generated/ingestion/config_schemas/ ${SCHEMA_ROOT_DIR}/${SCHEMA_FILE_NAME}

```

## 跳过测试

因测试用例运行比较慢，构建的时候可以选择跳过：

```Shell
./gradlew build -x test
```

## Task :datahub-web-react:yarnInstall

```Log
> Task :datahub-web-react:yarnInstall
info There appears to be trouble with your network connection. Retrying...
info If you think this is a bug, please open a bug report with the information provided in "/home/zhangjc/github/datahub/datahub-web-react/yarn-error.log".
info Visit https://yarnpkg.com/en/docs/cli/install for documentation about this command.
error An unexpected error occurred: "https://registry.yarnpkg.com/monaco-editor/-/monaco-editor-0.28.1.tgz: ESOCKETTIMEDOUT".
```

设置yarn代理：

```Shell
yarn config set registry https://registry.npm.taobao.org/
```

## Module "boto3" has no attribute "Session"

参见：(https://github.com/datahub-project/datahub/issues/5475)

## Task :metadata-ingestion:testQuick FAILED

参见：(https://github.com/datahub-project/datahub/issues/5481)