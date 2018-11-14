---
title: 诊断 Azure Cosmos DB Java 异步 SDK 并对其进行故障排除 | Microsoft Docs
description: 使用客户端日志记录和其他第三方工具等功能来确定、诊断和排查 Azure Cosmos DB 问题。
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: 74813634aad95f163b06717521bb2c746ac3df6b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238823"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>排除将 Java 异步 SDK 与 Azure Cosmos DB SQL API 帐户一同使用时出现的故障
本文介绍了将 [Java 异步 ADK](sql-api-sdk-async-java.md) 与 Azure Cosmos DB SQL API 帐户一同使用时的常见问题、解决方法、诊断步骤和工具。
Java 异步 SDK 提供客户端逻辑表示以访问 Azure Cosmos DB SQL API。 本文介绍了在遇到任何问题时可以提供帮助的工具和方法。

从本列表开始：
    * 请查看本文中的[常见问题和解决方法]部分。
    * SDK [在 github 上是开源的](https://github.com/Azure/azure-cosmosdb-java)，我们有需要积极监视的[问题部分](https://github.com/Azure/azure-cosmosdb-java/issues)。 检查在已提交的解决方法中是否发现任何类似问题。
    * 查看[性能提示](performance-tips-async-java.md)并按照建议的做法进行操作。
    * 按照本文的其余部分操作，如果找不到解决方案，请提交 [GitHub 问题](https://github.com/Azure/azure-cosmosdb-java/issues)。

## <a name="common-issues-workarounds"></a>常见问题和解决方法

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>网络问题、Netty 读取超时故障、低吞吐量、高延迟

#### <a name="general-suggestions"></a>常规建议
* 确保应用与 Cosmos DB 帐户在同一区域运行。 
* 检查运行应用的主机 CPU 使用情况。 如果 CPU 使用率为 90% 或更高，请考虑在具有更高配置的主机上运行应用或将负载分发到更多计算机。

#### <a name="connection-throttling"></a>连接限制
因[主机上的连接限制]或 [Azure SNAT (PAT) 端口耗尽]，可能会出现连接限制：

##### <a name="connection-limit-on-host"></a>主机上的连接限制
某些 Linux 系统（例如 Red Hat）具有打开文件数的总数上限。 Linux 中的套接字以文本形式实现，因此这个数字也限制了连接总数。
运行以下命令：

```bash
ulimit -a
```
打开文件的数量（“nofile”）需要足够大（至少是连接池大小的两倍）。 请阅读[性能提示](performance-tips-async-java.md)了解详细信息。

##### <a name="snat"></a>Azure SNAT (PAT) 端口耗尽

如果应用部署在没有公共 IP 地址的 Azure VM 上，则默认情况下 [Azure SNAT 端口](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)用于建立与 VM 外部任何终结点的连接。 从 VM 到 Cosmos DB 终结点，允许的连接数受 [Azure SNAT 配置](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)的限制。

仅当 Azure VM 具有专用 IP 地址且来自 VM 的进程尝试与公共 IP 地址建立连接时，才使用 Azure SNAT 端口。 因此，有两种解决方法可以避免 Azure SNAT 限制：
    * 按照[启用 VNET 服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)中的说明，将 Azure Cosmos DB 服务终结点添加到 Azure VM VNET 的子网中。 启用服务终结点后，请求不再从公共 IP 发送到 cosmos DB，而是发送 VNET 和子网标识。 如果仅允许公共 IP，则此更改可能导致防火墙丢失。 如果使用防火墙，则在启用服务终结点时，使用 [VNET ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) 将子网添加到防火墙。
    * 将公共 IP 分配给 Azure VM。

#### <a name="http-proxy"></a>Http 代理

如果使用 HttpProxy，请确保 HttpProxy 支持 SDK `ConnectionPolicy` 中配置的连接数。
否则，将遇到连接问题。

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>无效的编码模式：阻塞性 Netty IO 线程

SDK 使用 [Netty](https://netty.io/) IO 库与 Azure Cosmos DB 服务进行通信。 我们拥有异步 API，并使用 netty 的非阻塞性 IO API。 SDK 的 IO 工作在 IO netty 线程上执行。 IO netty 线程的数量配置为与应用计算机的 CPU 核心数相同。 Netty IO 线程仅用于非阻塞性 netty IO 工作。 SDK 将其中一个 netty IO 线程上的 API 调用结果返回至应用代码。 如果应用在接收 netty 线程上的结果后对 netty 线程执行持久操作，则可能导致 SDK 没有足够数量的 IO 线程来执行其内部 IO 工作。 这种应用编码可能导致低吞吐量、高延迟和 `io.netty.handler.timeout.ReadTimeoutException` 故障。 解决方法是在知道操作需耗费时间的情况下切换线程。

   例如，下面的代码片段显示，如果在 netty 线程上执行持续时间超过几毫秒的工作，最终可能会陷入没有 netty IO 线程来处理 IO 工作的状态，并且会引发 ReadTimeoutException：
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   解决方法是更改执行耗时工作的线程。 为应用定义计划程序的单一实例：
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   每当需要处理耗时的工作时（例如，计算繁重的工作、阻塞性 IO），请使用 `.observeOn(customScheduler)` API 将线程切换到 `customScheduler` 提供的辅助角色。
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
通过使用 `observeOn(customScheduler)`，可以释放 netty IO 线程并切换到 customScheduler 提供的自定义线程。 此修改将解决此问题，不会再出现 `io.netty.handler.timeout.ReadTimeoutException` 故障。

### <a name="connection-pool-exhausted-issue"></a>连接池耗尽问题

`PoolExhaustedException` 是客户端故障。 如果经常出现此故障，则表明应用工作负载高于 SDK 连接池可以提供的工作负载。 增加连接池大小或将负载分发到多个应用可能会有所帮助。

### <a name="request-rate-too-large"></a>请求速率过大
此故障是服务器端故障，表示消耗了预配的吞吐量，应该稍后重试。 如果经常遇到这种故障，请考虑增加集合的吞吐量。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>连接到 Azure Cosmos DB 仿真器时出现故障

Cosmos DB 仿真器 HTTPS 证书是自签名证书。 要将 SDK 与仿真器一起使用，应该将仿真器证书导入 Java TrustStore。 如[此处](local-emulator-export-ssl-certificates.md)所述。


## <a name="enable-client-sice-logging"></a>启用客户端 SDK 日志记录

异步 Java SDK 使用 SLF4j 作为日志外观，支持记录到常用的日志框架，如 log4j 和 logback。

例如，如果要使用 log4j 作为日志记录框架，请在 Java 类路径中添加以下库：

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

并添加 log4j 配置：
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

有关详细信息，请查看 [sfl4j 日志记录手册](https://www.slf4j.org/manual.html)。

## <a name="netstats"></a>OS 网络统计信息
运行 netstat 命令，了解处于 `Established`、`CLOSE_WAIT` 等状态的连接数。

在 Linux 上可以运行以下命令：
```bash
netstat -nap
```
筛选结果，以仅显示与 Cosmos DB 终结点的连接。

显然，`Established` 状态下 Cosmos DB 终结点的连接数不应大于配置的连接池大小。

如果 `CLOSE_WAIT` 状态下有多个与 Cosmos DB 终结点的连接，例如超过 1000 个连接，则表明连接的建立和断开速度都非常快，这可能会导致问题。 有关详细信息，请查看[常见问题和解决方法]部分。

 <!--Anchors-->
[常见问题和解决方法]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[主机上的连接限制]: #connection-limit-on-host
[Azure SNAT (PAT) 端口耗尽]: #snat


