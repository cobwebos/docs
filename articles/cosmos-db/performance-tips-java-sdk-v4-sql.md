---
title: Azure Cosmos DB Java SDK v4 性能提示
description: 了解用于提高 Java SDK v4 的 Azure Cosmos 数据库性能的客户端配置选项
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.openlocfilehash: c6ff105a03181b588a9074675c97930696ac5e87
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850208"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4 性能提示

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> 本文中的性能提示仅适用于 Azure Cosmos DB Java SDK v4。 请查看 Azure Cosmos DB Java SDK v4 [发行说明](sql-api-sdk-java-v4.md)、[Maven 存储库](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 [故障排除指南](troubleshoot-java-sdk-v4-sql.md)了解详细信息。 如果你当前使用的是早于 v4 的版本，请参阅[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，获取升级到 v4 的相关帮助。
>

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用或 SDK 方法调用一样简单。 但是，由于 Azure Cosmos DB 是通过网络调用访问的，因此，使用 Azure Cosmos DB Java SDK v4 时，可以通过客户端优化获得最高性能。

如果有“如何改善数据库性能？”的疑问， 请考虑以下选项：

## <a name="networking"></a>网络

* **连接模式：使用直接模式**
<a id="direct-connection"></a>
    
    客户端连接到 Azure Cosmos DB 的方式对性能有重大影响（尤其在客户端延迟方面）。 ConnectionMode 是可用于配置客户端 ConnectionPolicy 的关键配置设置 。 对于 Azure Cosmos DB Java SDK v4，有两种可用的 ConnectionMode：  
      
    * [网关（默认值）](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [直接](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    这两个 ConnectionMode 实质上设置了请求从客户端计算机发往 Azure Cosmos DB 后端的分区时所采用的路由的条件。 通常，“直接”模式是获得最佳性能的首选选项 - 它允许客户端打开直接连接到 Azure Cosmos DB 后端分区的 TCP 连接，在不通过中介的情况下直接发送请求。 与之相反，在“网关”模式下，客户端发出的请求会路由到 Azure Cosmos DB 前端中所谓的“网关”服务器，该服务器接下来会将你的请求扇出到 Azure Cosmos DB 后端的相应分区。 如果应用程序在有严格防火墙限制的企业网络中运行，则“网关”模式是最佳选择，因为它使用标准 HTTPS 端口与单个终结点。 但是，对于性能的影响是每次从/向 Azure Cosmos DB 读取/写入数据时，“网关”模式都涉及到额外的网络跃点（从客户端到网关，以及从网关到分区）。 因此，直接模式因为网络跃点较少，可以提供更好的性能。

    ConnectionMode 是在构造 Azure Cosmos DB 客户端实例期间使用 ConnectionPolicy 参数配置的： 
    
   #### <a name="async"></a>[异步](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sync](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* **将客户端并置在同一 Azure 区域内以提高性能** <a id="same-region"></a>

    如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在与 Azure Cosmos 数据库所在的相同区域中。 通过大致的比较发现，在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅 [Azure Regions](https://azure.microsoft.com/regions/#services)（Azure 区域）。

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 连接策略演示" border="false":::

    与多区域 Azure Cosmos DB 帐户交互的应用需要配置[首选位置](tutorial-global-distribution-sql-api.md#preferred-locations)，以确保请求进入并置区域。

* **在 Azure VM 上启用“加速网络”以降低延迟。**

建议你按说明在 [Windows（单击获取说明）](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)或 [Linux（单击获取说明）](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)Azure VM 中启用“加速网络”，以便最大程度地提高性能。

没有加速网络，在 Azure VM 与其他 Azure 资源之间传输的 IO 可能会不必要地通过主机和虚拟交换机（位于 VM 与其网卡之间）进行路由。 在数据路径中以内联方式放置主机和虚拟交换机不仅会增加信道中的延迟和抖动，还会占用 VM 的 CPU 周期。 使用加速网络时，VM 直接与 NIC 连接，没有中介；以前由主机和虚拟交换机处理的任何网络策略细节现在都在 NIC 的硬件中处理；主机和虚拟交换机将被绕过。 通常情况下，当启用加速网络后，应会降低延迟并提高吞吐量，同时会提高延迟一致性并降低 CPU 利用率。

限制：加速网络必须受 VM OS 支持，并且只能在已停止并解除分配 VM 的情况下启用。 不能通过 Azure 资源管理器部署此 VM。

有关更多详细信息，请参阅 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 和 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 说明。

## <a name="sdk-usage"></a>SDK 用法
* **安装最新的 SDK**

    Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 页以了解最新的 SDK 并查看改进内容。

* **在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

    每个 Azure Cosmos DB 客户端实例都是线程安全的，可执行高效的连接管理和地址缓存。 若要通过 Azure Cosmos DB 客户端实现高效的连接管理和更好的性能，建议在应用程序生存期内对每个 AppDomain 使用单个 Azure Cosmos DB 客户端实例。

   <a id="max-connection"></a>

* **使用应用程序所需的最低一致性级别**

    创建 CosmosClient 时，在未显式设置的情况下，所使用的默认一致性是“会话”。 如果应用程序逻辑不要求“会话”一致性，请将“一致性”设置为“最终”。 注意：建议在采用 Azure Cosmos DB 更改源处理器的应用程序中至少使用“会话”一致性。

* **使用异步 API 最大化预配的吞吐量**

    Azure Cosmos DB Java SDK v4 捆绑了两个 API：同步 API 和异步 API。 大致说来，异步 API 用于实现 SDK 功能，而同步 API 则是一种精简的包装器，用于向异步 API 发出阻止调用。 这不同于较旧的 Azure Cosmos DB Async Java SDK v2（仅限异步），也不同于较旧的 Azure Cosmos DB Sync Java SDK v2（仅限同步，且具有完全不同的实现）。 
    
    API 的选择在客户端初始化期间确定；CosmosAsyncClient 支持异步 API，而 CosmosClient 支持同步 API。 
    
    异步 API 可实现非阻止 IO。如果你的目标是将请求发送到 Azure Cosmos DB 时最大化吞吐量，则它是最佳选择。 
    
    如果你想要或需要一个可以阻止对每个请求做出响应的 API，或者如果同步操作是应用程序中的主导模式，则使用同步 API 可能是正确的选择。 例如，在吞吐量并不重要的情况下，若要在微服务应用程序中将数据持久保存到 Azure Cosmos DB，则可以使用同步 API。  
    
    请注意，同步 API 吞吐量会随请求响应时间的增加而降低，而异步 API 可充分利用硬件的全部带宽能力。 
    
    使用同步 API 时，进行地理并置可以获得更高且更一致的吞吐量（请参阅[将客户端并置在同一 Azure 区域内以提高性能](#collocate-clients)），但应不会超过异步 API 可获得的吞吐量。

    另外，某些用户可能不熟悉 [Project Reactor](https://projectreactor.io/)，这是用于实现 Azure Cosmos DB Java SDK v4 异步 API 的反应流框架。 如果存在此问题，建议你阅读我们的简介性文章：[Reactor Pattern Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)（Reactor 模式指南），然后查看此[响应式编程简介](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro)，自行熟悉相关内容。 如果你已将 Azure Cosmos DB 与异步接口配合使用，并且所使用的 SDK 是 Azure Cosmos DB Async Java SDK v2，那么你可能已熟悉 [ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava)，但不确定 Project Reactor 中所做的变更。 这种情况下，请查看我们的 [Reactor vs.RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)（Reactor 与 RxJava 指南），熟悉相关内容。

    以下代码片段演示了如何分别针对异步 API 或同步 API 操作初始化 Azure Cosmos DB 客户端：

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[异步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 异步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **优化 ConnectionPolicy**

    默认情况下，在使用 Azure Cosmos DB Java SDK v4 时，直接模式 Cosmos DB 请求是通过 TCP 发出的。 在内部，SDK 使用特殊的直接模式体系结构来动态管理网络资源并获得最佳性能。

    在 Azure Cosmos DB Java SDK v4 中，直接模式是为大多数工作负荷改善数据库性能的最佳选择。 

    * ***直接模式概述***

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="直接模式体系结构插图" border="false":::

        在直接模式下采用的客户端体系结构使得网络利用率可预测，并实现对 Azure Cosmos DB 副本的多路访问。 上图显示了直接模式如何将客户端请求路由到 Cosmos DB 后端中的副本。 直接模式体系结构在客户端上为每个数据库副本最多分配 10 个通道。 一个通道是前面带有请求缓冲区（深度为 30 个请求）的 TCP 连接。 属于某个副本的通道由该副本的服务终结点按需动态分配。 当用户在直接模式下发出请求时，TransportClient 会根据分区键将请求路由到适当的服务终结点。 请求队列在服务终结点之前缓冲请求。

    * ***直接模式的 ConnectionPolicy 配置选项***

        以下配置设置控制 RNTBD 体系结构的行为，而该体系结构则控制直接模式 SDK 的行为。
        
        第一步是使用下面推荐的配置设置。 这些 ConnectionPolicy 选项是高级配置设置，可能会以意想不到的方式影响 SDK 性能；我们建议用户不要对其进行修改，除非他们深刻了解其中的得失，并且进行修改是绝对必要的。 如果遇到有关此特定主题方面的问题，请与 [Azure Cosmos DB 团队](mailto:CosmosDBPerformanceSupport@service.microsoft.com)联系。

        如果使用 Azure Cosmos DB 作为参考数据库（即，该数据库用于多个点读取操作和少量的写入操作），可以接受将 idleEndpointTimeout 设置为 0（即无超时）。


        | 配置选项       | 默认    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | connectionTimeout          | “PT1M”     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | "PT5S"     |
        | requestTimeout             | “PT1M”     |
        | requestTimerResolution     | "PT0.5S"   |
        | sendHangDetectionTime      | "PT10S"    |
        | shutdownTimeout            | "PT15S"    |

* **优化分区集合的并行查询。**

    Azure Cosmos DB Java SDK v4 支持并行查询，允许以并行方式查询分区的集合。 有关详细信息，请参阅与使用 Azure Cosmos DB Java SDK v4 相关的[代码示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)。 并行查询旨改善查询延迟和串行配对物上的吞吐量。

    * ***优化 setMaxDegreeOfParallelism\:***
    
        并行查询的方式是并行查询多个分区。 但就查询本身而言，会按顺序提取单个已分区集合中的数据。 因此，通过使用 setMaxDegreeOfParallelism 设置分区数，最有可能实现查询的最高性能，但前提是所有其他系统条件仍保持不变。 如果不知道分区数，可使用 setMaxDegreeOfParallelism 设置一个较高的数值，系统会选择最小值（分区数、用户输入）作为最大并行度。

        必须注意，如果查询时数据均衡分布在所有分区之间，则并行查询可提供最大的优势。 如果对分区集合进行分区，其中全部或大部分查询所返回的数据集中于几个分区（最坏的情况下为一个分区），则这些分区会遇到查询的性能瓶颈。

    * ***优化 setMaxBufferedItemCount\:***
    
        并行查询设计为当客户端正在处理当前结果批时预提取结果。 预提取帮助改进查询中的的总体延迟。 setMaxBufferedItemCount 会限制预提取结果的数目。 通过将 setMaxBufferedItemCount 设置为预期返回的结果数（或较高的数值），可使查询从预提取获得最大的好处。

        预提取的工作方式不因 MaxDegreeOfParallelism 而异，并且有一个单独的缓冲区用来存储所有分区的数据。

* **增大客户端工作负荷**

    如果在高吞吐量级别进行测试，客户端应用程序可能会由于计算机的 CPU 或网络利用率达到上限而成为瓶颈。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

    建议不要让任何给定服务器上的 CPU 利用率超出 50%，使延迟保持在较低水平。

   <a id="tune-page-size"></a>

* **调整查询/读取源的页面大小以获得更好的性能**

    使用读取源功能（例如 *readItems*）执行批量文档读取时，或发出 SQL 查询 (*queryItems*) 时，如果结果集太大，则会以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

    假设应用程序向 Azure Cosmos DB 发出一个查询，同时假设应用程序需要有完整的查询结果集才能完成其任务。 若要减少检索所有适用结果所需的网络往返次数，可以通过调整 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 请求标头字段来增大页面大小。 

    * 对于单分区查询，将 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 字段值调整为 -1（对页面大小没有限制）可以最大程度地减少查询响应页的数目，从而最大程度地增加延迟，这样就会出现两种情况：一种是在单个页面中返回完整结果集；另一种是查询所用时间超出了超时时间间隔，因此将会以尽可能少的页数返回完整结果集。 这样可以成倍节省请求往返时间。
    
    * 对于跨分区查询，如果将 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 字段设置为 -1 并去除页面大小限制，则会存在因无法管理的页面大小而使 SDK 无法正常工作的风险。 在跨分区的情况下，建议你将页面大小限制提高到某个够大但又有限的值（例如 1000），以降低延迟。 
    
    在某些应用程序中，可能不需要完整的查询结果集。 在只需要显示几个结果的情况下（例如，用户界面或应用程序 API 一次只返回 10 个结果），也可以将页面大小缩小到 10，以降低读取和查询所耗用的吞吐量。

    也可以设置 byPage 方法的首选页面大小参数，而不是直接修改 REST 标头字段。 请记住，[x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 或 *byPage* 的首选页面大小参数仅设置页面大小的上限，而不是绝对要求。因此，由于各种原因，你可能会看到 Azure Cosmos DB 返回的页面小于首选页面大小。 

* **使用相应的计划程序（避免窃取事件循环 IO Netty 线程）**

    Azure Cosmos DB Java SDK 的异步功能基于 [netty](https://netty.io/) 非阻止 IO。 SDK 使用固定数量的 IO netty 事件循环线程（数量与计算机提供的 CPU 核心数相同）来执行 IO 操作。 API 返回的 Flux 会将结果发送到某个共享 IO 事件循环 netty 线程上。 因此，切勿阻塞共享的 IO 事件循环 netty 线程。 针对 IO 事件循环 netty 线程执行 CPU 密集型工作或者阻塞操作可能导致死锁，或大大减少 SDK 吞吐量。

    例如，以下代码针对事件循环 IO netty 线程执行 CPU 密集型工作：
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    收到结果后，如果想要针对结果执行 CPU 密集型工作，应避免针对事件循环 IO netty 线程执行。 你可以改为提供自己的计划程序，以便提供自己的线程来运行工作，如下所示（需要 `import reactor.core.scheduler.Schedulers`）。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    应该根据工作的类型使用相应的现有 Reactor 计划程序来执行工作。 请阅读 [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)。

    有关 Azure Cosmos DB Java SDK v4 的详细信息，请参阅 [GitHub 上 Azure SDK for Java 单存储库的 Cosmos DB 目录](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)。

* **优化应用程序中的日志记录设置**

    由于各种原因，你可能希望或需要在某个产生较高请求吞吐量的线程中添加日志记录。 如果你的目标是使用此线程生成的请求使容器的预配吞吐量完全饱和，则日志记录优化可以极大地提升性能。

    * 配置异步记录器

        生成请求的线程的总体延迟计算必然会考虑到同步记录器延迟的因素。 建议使用异步记录器（例如 [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0)），以便将日志记录开销与高性能应用程序线程分开。

    * ***禁用 netty 的日志记录***

        Netty 库日志记录非常琐碎，因此需要将其关闭（在配置中禁止登录可能并不足够），以避免产生额外的 CPU 开销。 如果不处于调试模式，请一起禁用 netty 日志记录。 因此，如果要使用 log4j 来消除 netty 中 ``org.apache.log4j.Category.callAppenders()`` 产生的额外 CPU 开销，请将以下行添加到基代码：

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **OS 打开文件资源限制**
 
    某些 Linux 系统（例如 Red Hat）对打开的文件数和连接总数施加了上限。 运行以下命令以查看当前限制：

    ```bash
    ulimit -a
    ```

    打开的文件数 (nofile) 需要足够大，以便为配置的连接池大小和 OS 打开的其他文件留出足够的空间。 可以修改此参数，以增大连接池大小。

    打开 limits.conf 文件：

    ```bash
    vim /etc/security/limits.conf
    ```
    
    添加/修改以下行：

    ```
    * - nofile 100000
    ```

* **在点写入中指定分区键**

    若要提高点写入的性能，请在点写入 API 调用中指定项分区键，如下所示：

    # <a name="async"></a>[异步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 异步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    而不是仅提供项实例，如下所示：

    # <a name="async"></a>[异步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 异步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    后者是受支持的，但会增加应用程序的延迟；SDK 必须分析项并提取分区键。

## <a name="indexing-policy"></a>索引策略
 
* **从索引中排除未使用的路径以加快写入速度**

    Azure Cosmos DB 的索引策略允许使用索引路径（setIncludedPaths 和 setExcludedPaths）指定要在索引中包括或排除的文档路径。 在事先知道查询模式的方案中，使用索引路径可改善写入性能并降低索引存储空间，因为索引成本与索引的唯一路径数目直接相关。 例如，以下代码演示如何使用“*”通配符从索引中排除整个文档部分（也称为子树）。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com.azure::azure-cosmos)
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    有关详细信息，请参阅 [Azure Cosmos DB 索引策略](indexing-policies.md)。

## <a name="throughput"></a>吞吐量
<a id="measure-rus"></a>

* **测量和优化较低的每秒请求单位使用量**

    Azure Cosmos DB 提供一组丰富的数据库操作，包括 UDF 的关系和层次查询、存储过程和触发 – 所有都在数据库集合的文档上操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 与考虑和管理硬件资源不同的是，可以考虑将请求单位 (RU) 作为所需资源的单个措施，以执行各种数据库操作和服务应用程序请求。

    吞吐量是基于为每个容器设置的[请求单位](request-units.md)数量预配的。 请求单位消耗以每秒速率评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。

    查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

    若要测量任何操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 标头来测量这些操作占用的请求单位数。 也可以在 ResourceResponse\<T> 或 FeedResponse\<T> 中找到等效的 RequestCharge 属性。

    # <a name="async"></a>[异步](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) 异步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[Sync](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) 同步 API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    在此标头中返回的请求费用是预配吞吐量的一小部分。 例如，如果预配了 2000 RU/s，上述查询返回 1000 个 1KB 文档，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。

<a id="429"></a>
* **处理速率限制/请求速率太大**

    客户端尝试超过帐户保留的吞吐量时，服务器的性能不会降低，并且不会使用超过保留级别的吞吐量容量。 服务器将抢先结束 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 标头，该标头指示重新尝试请求前用户必须等待的时间量（以毫秒为单位）。

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

    如果累计有多个客户端持续在超过请求速率的情况下运行，则当前由客户端在内部设置为 9 的默认重试计数可能并不足够；在此情况下，客户端就会向应用程序引发 CosmosClientException，其状态代码为 429。 可以通过在 ConnectionPolicy 实例上使用 setRetryOptions 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则会在 30 秒的累积等待时间后返回 CosmosClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（默认值 9 或用户定义的值），也会发生这种情况。

    尽管自动重试行为有助于改善大多数应用程序的复原能力和可用性，但是在执行性能基准测试时可能会造成冲突（尤其是在测量延迟时）。 如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。

* **针对小型文档进行设计以提高吞吐量**

    给定操作的请求费用（请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。 最好在设计应用程序和工作流的架构时，将项大小设为大约 1KB 或类似的数量级。 对于延迟敏感型应用程序，应避免出现大项 - 大型文档会降低应用程序的速度。

## <a name="next-steps"></a>后续步骤

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
