---
title: 诊断 Azure Cosmos DB Java SDK v4 并对其进行故障排除
description: 使用客户端日志记录等功能和其他第三方工具来确定、诊断和排查 Java SDK v4 中的 Azure Cosmos DB 问题。
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.openlocfilehash: 2deec6f6753a03ab46260432c6faceab009e2911
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651870"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>对将 Azure Cosmos DB Java SDK v4 与 SQL API 帐户配合使用时出现的问题进行故障排除

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> 本文仅介绍 Azure Cosmos DB Java SDK v4 的故障排除。 有关详细信息，请参阅 Azure Cosmos DB Java SDK v4 [发行说明](sql-api-sdk-java-v4.md)、[Maven 存储库](https://mvnrepository.com/artifact/com.azure/azure-cosmos)和[性能提示](performance-tips-java-sdk-v4-sql.md)。 如果你当前使用的是早于 v4 的版本，请参阅[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，获取升级到 v4 的相关帮助。
>

本文介绍将 Azure Cosmos DB Java SDK v4 与 Azure Cosmos DB SQL API 帐户配合使用时遇到的常见问题、解决方法、诊断步骤和工具。
Azure Cosmos DB Java SDK v4 提供客户端逻辑表示来访问 Azure Cosmos DB SQL API。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

从本列表开始：

* 请查看本文中的[常见问题和解决方法]部分。
* 查看 Azure Cosmos DB 中央存储库中的 Java SDK，该存储库[在 GitHub 上是开源的](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)。 该 SDK 拥有受到主动监视的[问题部分](https://github.com/Azure/azure-sdk-for-java/issues)。 检查是否已提交包含解决方法的任何类似问题。 一个有用的提示是通过 cosmos:v4-item 标记来筛选问题。
* 查看 Azure Cosmos DB Java SDK v4 的[性能提示](performance-tips-java-sdk-v4-sql.md)并按照建议的做法进行操作。
* 阅读本文的其余部分，如果找不到解决方案， 则提交 [GitHub 问题](https://github.com/Azure/azure-sdk-for-java/issues)。 如果有将标记添加到 GitHub 问题的选项，请添加 cosmos:v4-item 标记。

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>网络问题、Netty 读取超时故障、低吞吐量、高延迟

#### <a name="general-suggestions"></a>常规建议
为获得最佳性能：
* 确保应用与 Azure Cosmos DB 帐户在同一区域运行。 
* 检查运行应用的主机 CPU 使用情况。 如果 CPU 使用率为 50% 或更高，请在具有更高配置的主机上运行应用。 或者，可将负载分发到更多计算机。
    * 如果在 Azure Kubernetes 服务上运行应用程序，则可以[使用 Azure Monitor 监视 CPU 使用率](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze)。

#### <a name="connection-throttling"></a>连接限制
连接限制可能会因[主机上的连接限制]或 [Azure SNAT (PAT) 端口耗尽]而出现。

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>主机上的连接限制
某些 Linux 系统（例如 Red Hat）的打开文件总数存在上限。 Linux 中的套接字以文件形式实现，因此，此上限也限制了连接总数。
运行以下命令。

```bash
ulimit -a
```
允许的最大打开文件数（标识为“nofile”）至少需要是连接池大小的两倍。 有关详细信息，请参阅 Azure Cosmos DB Java SDK v4 [性能提示](performance-tips-java-sdk-v4-sql.md)。

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在没有公共 IP 地址的 Azure 虚拟机上，则默认情况下，[Azure SNAT 端口](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)用于建立与 VM 外部任何终结点的连接。 从 VM 到 Azure Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)的限制。

 仅当 VM 具有专用 IP 地址且来自 VM 的进程尝试连接到公共 IP 地址时，才会使用 Azure SNAT 端口。 有两种解决方法可以避免 Azure SNAT 限制：

* 向 Azure 虚拟机虚拟网络的子网添加 Azure Cosmos DB 服务终结点。 有关详细信息，请参阅 [Azure 虚拟网络服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)。 

    启用服务终结点后，不再从公共 IP 向 Azure Cosmos DB 发送请求， 而是发送虚拟网络和子网标识。 如果仅允许公共 IP，则此更改可能会导致防火墙丢失。 如果使用防火墙，则在启用服务终结点后，请使用[虚拟网络 ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) 将子网添加到防火墙。
* 将公共 IP 分配给 Azure VM。

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>无法访问服务 - 防火墙
``ConnectTimeoutException`` 表示 SDK 无法访问服务。
使用直接模式时，可能会出现类似如下的错误：
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

如果应用计算机上有防火墙正在运行，请打开端口范围 10,000 到 20,000，这是直接模式使用的端口范围。
请遵循[主机上的连接限制](#connection-limit-on-host)。

#### <a name="http-proxy"></a>HTTP 代理

如果使用 HTTP 代理，请确保它支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>无效的编码模式：阻塞性 Netty IO 线程

SDK 使用 [Netty](https://netty.io/) IO 库与 Azure Cosmos DB 通信。 SDK 拥有异步 API 并使用 Netty 的非阻塞性 IO API。 SDK 的 IO 工作在 IO Netty 线程上执行。 IO Netty 线程的数量配置为与应用计算机的 CPU 核心数相同。 

Netty IO 线程仅用于非阻塞性 Netty IO 工作。 SDK 将其中一个 Netty IO 线程上的 API 调用结果返回至应用代码。 如果应用在收到 Netty 线程上的结果后执行持续时间较长的操作，则 SDK 可能会没有足够的 IO 线程来执行其内部 IO 工作。 此类应用编码可能会导致低吞吐量、高延迟和 `io.netty.handler.timeout.ReadTimeoutException` 故障。 解决方法是在知道操作需要耗费一定时间的情况下切换线程。

例如，请看下面的代码段，它可将项添加到容器中（请参阅[此处](create-sql-api-java.md)以获取有关设置数据库和容器的指南。）你可能会在 Netty 线程上执行耗时超过几毫秒的持续时间较长的工作。 在这种情况下，你最终会陷入没有 Netty IO 线程来处理 IO 工作的状态。 因此，你会遇到 ReadTimeoutException 故障。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
  int requestTimeoutInSeconds = 10;
  ConnectionPolicy policy = new ConnectionPolicy();
  policy.setRequestTimeout(Duration.ofMillis(requestTimeoutInSeconds * 1000));
  AtomicInteger failureCount = new AtomicInteger();
  // Max number of concurrent item inserts is # CPU cores + 1
  Flux<Family> familyPub = 
      Flux.just(Families.getAndersenFamilyItem(), Families.getWitherspoonFamilyItem(), Families.getCarltonFamilyItem());
  familyPub.flatMap(family -> {
      return container.createItem(family);
  }).flatMap(r -> {
      try {
          // Time-consuming work is, for example,
          // writing to a file, computationally heavy work, or just sleep.
          // Basically, it's anything that takes more than a few milliseconds.
          // Doing such operations on the IO Netty thread
          // without a proper scheduler will cause problems.
          // The subscriber will get a ReadTimeoutException failure.
          TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
      } catch (Exception e) {
      }
      return Mono.empty();
  }).doOnError(Exception.class, exception -> {
      failureCount.incrementAndGet();
  }).blockLast();
  assert(failureCount.get() > 0);
}
```

解决方法是更改用于执行需要耗费一定时间的工作的线程。 为应用定义计划程序的单一实例。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = Schedulers.fromExecutor(ex);
```
你可能会需要完成需耗费一定时间的工作，例如，计算工作量繁重的工作或阻塞性 IO。 在这种情况下，使用 `.publishOn(customScheduler)` API 将线程切换为 `customScheduler` 提供的辅助角色。

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

```java
container.createItem(family)
    .publishOn(customScheduler) // Switches the thread.
    .subscribe(
        // ...
    );
```
通过使用 `publishOn(customScheduler)`，可以释放 Netty IO 线程并切换到自定义计划程序提供的自定义线程。 此修改可解决这一问题。 你不会再遇到 `io.netty.handler.timeout.ReadTimeoutException` 故障。

### <a name="request-rate-too-large"></a>请求速率过大
此故障是服务器端故障。 它表明预配的吞吐量已用完。 请稍后重试。 如果经常遇到此故障，请考虑增加集合吞吐量。

* **按 getRetryAfterInMilliseconds 间隔实现回退**

    在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果受到限制，客户端应用程序应按照服务器指定的重试间隔退让。 遵循退让可确保最大程度地减少等待重试的时间。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>连接到 Azure Cosmos DB 仿真器时出现故障

Azure Cosmos DB 仿真器 HTTPS 证书是自签名证书。 若要将 SDK 与仿真器配合使用，请将仿真器证书导入 Java TrustStore。 有关详细信息，请参阅[导出 Azure Cosmos DB 仿真器证书](local-emulator-export-ssl-certificates.md)。

### <a name="dependency-conflict-issues"></a>依赖项冲突问题

Azure Cosmos DB Java SDK 会提取大量依赖项；一般来说，如果项目依赖关系树中包含 Azure Cosmos DB Java SDK 所依赖的旧项目版本，则这可能会导致在运行应用程序时生成意外错误。 如果你正在调试应用程序意外抛出异常的原因，最好重新检查依赖关系树是否意外地拉入了一个或多个 Azure Cosmos DB Java SDK 依赖项的旧版本。

解决此问题的方法是确定哪些项目依赖项引入了旧版本，并排除该旧版本上的可传递依赖项，然后允许 Azure Cosmos DB Java SDK 引入新版本。

要确定哪个项目依赖项引入了 Azure Cosmos DB Java SDK 所依赖的旧版本，请对项目 pom.xml 文件运行以下命令：
```bash
mvn dependency:tree
```
有关详细信息，请参阅[maven 依赖项树指南](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)。

了解项目的哪些依赖项依赖于较旧的版本后，可以在 pom 文件中修改该代码库的依赖项，并按照以下示例排除传递依赖项（假定 *reactor-core* 是过时的依赖项）：

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

有关详细信息，请参阅[排除传递依赖项指南](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)。


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>启用客户端 SDK 日志记录

Azure Cosmos DB Java SDK v4 使用 SLF4j 作为日志外观，支持记录到常用的日志框架，如 log4j 和 logback。

例如，如果要使用 log4j 作为日志记录框架，请在 Java classpath 中添加以下库。

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

同时添加 log4j 配置。
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

有关详细信息，请参阅 [sfl4j 日志记录手册](https://www.slf4j.org/manual.html)。

## <a name="os-network-statistics"></a><a name="netstats"></a>OS 网络统计信息
运行 netstat 命令，掌握处于 `ESTABLISHED` 和 `CLOSE_WAIT` 等状态的连接数。

在 Linux 上可以运行以下命令。
```bash
netstat -nap
```

在 Windows 上，可以使用不同的参数标志运行同一命令：
```bash
netstat -abn
```

筛选结果，以便仅显示到 Azure Cosmos DB 终结点的连接。

处于 `ESTABLISHED` 状态的 Azure Cosmos DB 终结点连接数不应大于配置的连接池大小。

到 Azure Cosmos DB 终结点的许多连接可能会处于 `CLOSE_WAIT` 状态。 可能会有超过 1,000 个连接。 较大的数字表明建立和销毁连接的速度很快。 这种情况可能会导致问题。 有关详细信息，请参阅[常见问题和解决方法]部分。

 <!--Anchors-->
[常见问题和解决方法]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[主机上的连接限制]: #connection-limit-on-host
[Azure SNAT (PAT) 端口耗尽]: #snat


