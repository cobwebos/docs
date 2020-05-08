---
title: Azure Cosmos DB Java SDK v4 的性能提示
description: 了解用于提高 Java SDK v4 的 Azure Cosmos 数据库性能的客户端配置选项
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982524"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4 的性能提示

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> 本文中的性能提示仅适用于 Azure Cosmos DB Java SDK v4。 有关详细信息，请查看 Azure Cosmos DB Java SDK v4 发行说明、 [Maven 存储库](https://mvnrepository.com/artifact/com.azure/azure-cosmos)和 AZURE COSMOS DB java sdk v4[故障排除指南](troubleshoot-java-sdk-v4-sql.md)。 如果你当前使用的是较旧版本，请参阅[迁移到 Azure Cosmos DB JAVA SDK v4](migrate-java-v4-sdk.md)指南，以帮助升级到 v4。
>

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用或 SDK 方法调用一样简单。 但是，由于通过网络调用访问 Azure Cosmos DB，因此，在使用 Azure Cosmos DB Java SDK v4 时，可以通过客户端优化来实现最高性能。

如果有“如何改善数据库性能？”的疑问， 请考虑以下选项：

## <a name="networking"></a>网络

* **连接模式：使用直接模式**
<a id="direct-connection"></a>
    
    客户端连接到 Azure Cosmos DB 的方式对性能有重大影响（尤其在客户端延迟方面）。 ConnectionMode 是可用于配置客户端 ConnectionPolicy 的关键配置设置   。 对于 Azure Cosmos DB Java SDK v4，两个可用的*ConnectionMode*是：  
      
    * [网关（默认值）](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [直接](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    这些*ConnectionMode*实质上是指请求从客户端计算机发送到 Azure Cosmos DB 后端中的分区的路由。 通常，直接模式是最佳性能的首选选项，它允许客户端直接打开与 Azure Cosmos DB 后端中的分区的 TCP 连接，并向无中介*直接*发送请求 ly。 与此相反，在网关模式下，客户端发出的请求会路由到 Azure Cosmos DB 前端中所谓的 "网关" 服务器，这进而会使风扇超出 Azure Cosmos DB 后端的相应分区的请求。 如果你的应用程序在具有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口和单个终结点。 然而，性能的折衷在于，每次读取或写入数据到 Azure Cosmos DB 时，网关模式都涉及到额外的网络跃点（客户端到网关，并与分区进行分区）。 因此，由于涉及的网络跃点较少，直接模式会提供更好的性能。

    在 Azure Cosmos DB 客户端实例与*ConnectionPolicy*参数的构造过程中配置*ConnectionMode* ：
    
   #### <a name="async"></a>[异步](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

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

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>Java SDK V4 （Maven：： azure-cosmos）同步 API

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

    ![Azure Cosmos DB 连接策略演示](./media/performance-tips/same-region.png)

    与多区域 Azure Cosmos DB 帐户交互的应用需要配置[首选位置]()，以确保请求进入并置区域。

* **在 Azure VM 上启用加速网络以降低延迟。**

建议按照说明在 Windows 中启用加速网络[（单击以获得说明）](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)或[Linux （单击以了解说明）](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Azure VM，以便最大程度地提高性能。

如果不使用加速网络，则在 Azure VM 与其他 Azure 资源之间上流动的 IO 可能会不必要地通过位于 VM 与其网卡之间的主机和虚拟交换机进行路由。 在数据路径中内联主机和虚拟交换机不仅会增加通信通道的延迟和抖动，还可以从 VM 盗取 CPU 周期。 对于加速网络，VM 直接连接到无中介的 NIC;主机和虚拟交换机正在处理的任何网络策略详细信息现在都在 NIC 的硬件中进行处理;将绕过主机和虚拟交换机。 通常，当你启用加速网络时，可能会降低延迟和提高吞吐量，同时*降低延迟和*CPU 使用率。

限制： VM 操作系统必须支持加速网络，并且只能在停止并释放 VM 时启用。 无法将 VM 与 Azure 资源管理器一起部署。

有关更多详细信息，请参阅[Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)和[Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)的说明。

## <a name="sdk-usage"></a>SDK 用法
* **安装最新的 SDK**

    Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 页以了解最新的 SDK 并查看改进内容。

* **在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

    每个 Azure Cosmos DB 客户端实例都是线程安全的，并执行高效的连接管理和地址缓存。 若要允许 Azure Cosmos DB 的客户端实现高效的连接管理和更好的性能，建议在应用程序的生存期内使用每个 AppDomain 的 Azure Cosmos DB 客户端的单个实例。

   <a id="max-connection"></a>

* **使用应用程序所需的最低一致性级别**

    当您创建*CosmosClient*时，如果未显式设置为*Session*，则使用默认一致性。 如果应用程序逻辑不需要*会话*一致性，请将*一致性*设置为 "*最终*"。 注意：建议在采用 Azure Cosmos DB 更改源处理器的应用程序中使用至少*会话*一致性。

* **使用异步 API 来最大值预配吞吐量**

    Azure Cosmos DB Java SDK v4 捆绑了两个 Api： Sync 和 Async。 大致说来，Async API 实现了 SDK 功能，而同步 API 是一种精简的包装器，它会阻止调用异步 API。 这与较旧的 Azure Cosmos DB Async Java SDK v2 不同，后者只是异步的，而对于较旧的 Azure Cosmos DB Sync Java SDK v2，它是仅同步的，并且具有完全不同的实现。 
    
    API 的选择是在客户端初始化期间确定的;*CosmosAsyncClient*支持异步 api，而*COSMOSCLIENT*支持同步 api。 
    
    如果你的目标是将请求发送到 Azure Cosmos DB，则异步 API 将实现非阻止 IO，这是最佳选择。 
    
    如果希望或需要在对每个请求的响应中阻止的 API，或者如果同步操作是应用程序中的主导模式，则使用同步 API 可能是正确的选择。 例如，你可能希望在微服务应用程序中将数据保存到 Azure Cosmos DB 时使用同步 API，但前提是吞吐量并不重要。  
    
    请注意，同步 API 吞吐量会随请求响应时间的增加而降低，而异步 API 可能会使硬件的全部带宽功能饱和。 
    
    使用同步 API 时，地理归置可以提供更高、更一致的吞吐量（请参阅[在同一 Azure 区域中的归置客户端性能](#collocate-clients)），但仍不应超过可实现异步 API 的吞吐量。

    某些用户可能还不熟悉[Project 反应器](https://projectreactor.io/)，这是用于实现 Java SDK V4 异步 API Azure Cosmos DB 反应流框架。 如果这是一个问题，我们建议您阅读我们的介绍性[反应器模式指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)，然后查看关于[反应编程](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro)的这一简介，以便您熟悉。 如果已将 Azure Cosmos DB 与异步接口一起使用，并且所使用的 SDK Azure Cosmos DB async Java SDK v2，则可能会熟悉[ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava) ，但不确定项目反应器中的更改。 在这种情况下，请查看我们的[反应器与 RxJava 指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)以获得熟悉。

    以下代码片段演示了如何分别为异步 API 或同步 API 操作初始化 Azure Cosmos DB 客户端：

    #### <a name="async"></a>[异步](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>Java SDK V4 （Maven：： azure-cosmos）同步 API

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **优化 ConnectionPolicy**

    默认情况下，使用 Azure Cosmos DB Java SDK v4 时，直接模式 Cosmos DB 通过 TCP 进行请求。 在内部，SDK 使用特殊的直接模式体系结构来动态管理网络资源并获得最佳性能。

    在 Azure Cosmos DB Java SDK v4 中，直接模式是提高大多数工作负荷的数据库性能的最佳选择。 

    * ***直接模式概述***

        ![直接模式体系结构插图](./media/performance-tips-async-java/rntbdtransportclient.png)

        在直接模式下采用的客户端体系结构使得网络利用率可预测，并实现对 Azure Cosmos DB 副本的多路访问。 上图显示了直接模式如何将客户端请求路由到 Cosmos DB 后端中的副本。 直接模式体系结构在客户端上为每个数据库副本最多分配 10 个通道  。 一个通道是前面带有请求缓冲区（深度为 30 个请求）的 TCP 连接。 属于某个副本的通道由该副本的服务终结点按需动态分配  。 当用户在直接模式下发出请求时，TransportClient 会根据分区键将请求路由到适当的服务终结点  。 请求队列在服务终结点之前缓冲请求  。

    * ***直接模式的 ConnectionPolicy 配置选项***

        这些配置设置控制 RNTBD 体系结构的行为，该体系结构控制直接模式 SDK 的行为。
        
        第一步是使用下面推荐的配置设置。 这些*ConnectionPolicy*选项是高级配置设置，可能会以意外的方式影响 SDK 性能;建议用户不要对其进行修改，除非他们感到非常乐于理解折衷方案，并且这是绝对必要的。 如果遇到有关此特定主题方面的问题，请与 [Azure Cosmos DB 团队](mailto:CosmosDBPerformanceSupport@service.microsoft.com)联系。

        如果使用 Azure Cosmos DB 作为参考数据库（即，该数据库用于多个点读取操作和少量的写入操作），可以接受将 idleEndpointTimeout 设置为 0（即无超时）  。


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

    Azure Cosmos DB Java SDK v4 支持并行查询，这使你能够并行查询分区集合。 有关详细信息，请参阅与使用 Azure Cosmos DB Java SDK v4 相关的[代码示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)。 并行查询旨改善查询延迟和串行配对物上的吞吐量。

    * ***优化 setMaxDegreeOfParallelism\:***
    
        并行查询的工作原理是并行查询多个分区。 但就查询本身而言，会按顺序提取单个已分区集合中的数据。 因此，通过使用 setMaxDegreeOfParallelism 设置分区数，最有可能实现查询的最高性能，但前提是所有其他系统条件仍保持不变。 如果不知道分区数，可使用 setMaxDegreeOfParallelism 设置一个较高的数值，系统会选择最小值（分区数、用户输入）作为最大并行度。

        必须注意，如果查询时数据均衡分布在所有分区之间，则并行查询可提供最大的优势。 如果对分区集合进行分区，其中全部或大部分查询所返回的数据集中于几个分区（最坏的情况下为一个分区），则这些分区会遇到查询的性能瓶颈。

    * ***优化 setMaxBufferedItemCount\:***
    
        并行查询设计为当客户端正在处理当前结果批时预提取结果。 预提取帮助改进查询中的的总体延迟。 setMaxBufferedItemCount 会限制预提取结果的数目。 通过将 setMaxBufferedItemCount 设置为预期返回的结果数（或较高的数值），可使查询从预提取获得最大的好处。

        预提取的工作方式不因 MaxDegreeOfParallelism 而异，并且有一个单独的缓冲区用来存储所有分区的数据。

* **增大客户端工作负荷**

    如果以高吞吐量级别进行测试，则客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率将达到上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

    很好的经验法则并不会超过任何给定服务器上 >50% 的 CPU 使用率，从而使延迟较低。

   <a id="tune-page-size"></a>

* **调整查询/读取源的页面大小以获得更好的性能**

    使用读取源功能（例如*readItems*）执行批量文档读取时，或发出 SQL 查询（*queryItems*）时，如果结果集太大，则以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

    假设您的应用程序发出一个查询以 Azure Cosmos DB，并假设您的应用程序需要一组完整的查询结果才能完成其任务。 若要减少检索所有适用的结果所需的网络往返次数，可以通过调整 " [x-最大值-最大项数](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)请求标头" 字段来增加页面大小。 

    * 对于单分区查询，将 "[最大](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)项数" 字段值调整为-1 （对页面大小没有限制）可以最大程度地减少延迟，这是因为完整结果集将在单个页面中返回，或者如果查询所用的时间超过超时间隔，则将以最小可用页数返回完整结果集。 这会节省请求往返时间的倍数。
    
    * 对于跨分区查询，将 "[最大项数-最大项数](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)" 字段设置为-1，删除页面大小限制会导致 SDK 的页面大小不受控制。 在跨分区情况下，我们建议将页面大小限制提高到一些大但有限的值（可能为1000），以减少延迟。 
    
    在某些应用程序中，可能不需要完整的查询结果集。 如果你只需要显示几个结果（例如，你的用户界面或应用程序 API 一次只返回10个结果），则还可以将页面大小缩小为10，以减少读取和查询使用的吞吐量。

    还可以设置*byPage*方法的首选页面大小参数，而不是直接修改 REST 标头字段。 请记住， *byPage*的[最大项数](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)或首选页面大小参数只设置页面大小的上限，而不是绝对要求。由于各种原因，你可能会看到 Azure Cosmos DB 返回的页面小于首选页面大小。 

* **使用相应的计划程序（避免窃取事件循环 IO Netty 线程）**

    Azure Cosmos DB Java SDK 的异步功能基于[netty](https://netty.io/)非阻止 IO。 SDK 使用固定数量的 IO netty 事件循环线程（数量与计算机提供的 CPU 核心数相同）来执行 IO 操作。 API 返回的 Flux 在某个共享 IO 事件循环 netty 线程上发出结果。 因此，切勿阻塞共享的 IO 事件循环 netty 线程。 针对 IO 事件循环 netty 线程执行 CPU 密集型工作或者阻塞操作可能导致死锁，或大大减少 SDK 吞吐量。

    例如，以下代码针对事件循环 IO netty 线程执行 CPU 密集型工作：
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    收到结果后，如果想要针对结果执行 CPU 密集型工作，应避免针对事件循环 IO netty 线程执行。 你可以提供自己的计划程序以提供自己的线程来运行你的工作，如下所示。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    根据你的工作的类型，你应使用适用于你的工作的现有反应器计划程序。 请阅读 [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)。

    有关 Azure Cosmos DB Java SDK v4 的详细信息，请参阅[GitHub 上的 AZURE SDK For Java monorepo 的 Cosmos DB 目录](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)。

* **优化应用程序中的日志记录设置**

    由于各种原因，你可能希望或需要在生成高请求吞吐量的线程中添加日志记录。 如果你的目标是使用此线程生成的请求来完全使容器的预配吞吐量饱和，则日志记录优化可以极大地提高性能。

    * ***配置异步记录器***

        同步记录器的延迟是请求生成线程的总体延迟计算的一部分。 建议使用异步记录器（如[log4j2.xml](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) ）将日志记录开销与高性能应用程序线程分离。

    * ***禁用 netty 的日志记录***

        Netty 库日志记录非常琐碎，因此需要将其关闭（在配置中禁止登录可能并不足够），以避免产生额外的 CPU 开销。 如果不处于调试模式，请一起禁用 netty 日志记录。 因此，如果要使用 log4j 来消除 netty 中 ``org.apache.log4j.Category.callAppenders()`` 产生的额外 CPU 开销，请将以下行添加到基代码：

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **OS 打开文件资源限制**
 
    某些 Linux 系统（如 Red Hat）对打开的文件的数量和连接总数有上限。 运行以下命令以查看当前限制：

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

    #### <a name="async"></a>[异步](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>Java SDK V4 （Maven：： azure-cosmos）同步 API

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    而不是仅提供 item 实例，如下所示：

    #### <a name="async"></a>[异步](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>Java SDK V4 （Maven：： azure-cosmos）同步 API

    ```java
    syncContainer.createItem(item);
    ```

    ---

    后者是受支持的，但会增加应用程序的延迟时间;SDK 必须分析项并提取分区键。

## <a name="indexing-policy"></a>索引策略
 
* **从索引中排除未使用的路径以加快写入速度**

    Azure Cosmos DB 的索引策略允许使用索引路径（setIncludedPaths 和 setExcludedPaths）指定要在索引中包括或排除的文档路径。 在事先知道查询模式的方案中，使用索引路径可改善写入性能并降低索引存储空间，因为索引成本与索引的唯一路径数目直接相关。 例如，以下代码演示如何使用“*”通配符从索引编制中排除文档的整个部分（也称为子树）。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 （Maven：： azure-cosmos）
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    有关索引的详细信息，请参阅 [Azure Cosmos DB 索引策略](indexing-policies.md)。

## <a name="throughput"></a>吞吐量
<a id="measure-rus"></a>

* **测量和优化较低的每秒请求单位使用量**

    Azure Cosmos DB 提供一组丰富的数据库操作，包括 UDF 的关系和层次查询、存储过程和触发 – 所有都在数据库集合的文档上操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 与考虑和管理硬件资源不同的是，可以考虑将请求单位 (RU) 作为所需资源的单个措施，以执行各种数据库操作和服务应用程序请求。

    吞吐量是基于为每个容器设置的[请求单位](request-units.md)数量预配的。 请求单位消耗以每秒速率评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。

    查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

    若要测量任何操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 标头来测量这些操作占用的请求单位数。 还可以查看 ResourceResponse\<T> 或 FeedResponse\<T> 中等效的 RequestCharge 属性。

    #### <a name="async"></a>[异步](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>Java SDK V4 （Maven：： azure-cosmos） Async API

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[同步](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>Java SDK V4 （Maven：： azure-cosmos）同步 API    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    在此标头中返回的请求费用是预配吞吐量的一小部分。 例如，如果预配了 2000 RU/s，上述查询返回 1000 个 1KB 文档，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。

<a id="429"></a>
* **处理速率限制/请求速率太大**

    客户端尝试超过帐户保留的吞吐量时，服务器的性能不会降低，并且不会使用超过保留级别的吞吐量容量。 服务器将抢先结束 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 标头，该标头指示重新尝试请求前用户必须等待的时间量（以毫秒为单位）。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

    如果有多个客户端的累积操作持续超过请求速率，则当前在客户端内部设置为9的默认重试计数可能无法满足需要;在这种情况下，客户端将向应用程序引发状态代码为429的*CosmosClientException* 。 可以通过在 ConnectionPolicy 实例上使用 setRetryOptions 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则会在30秒的累积等待时间后返回具有状态代码429的*CosmosClientException* 。 即使当前的重试计数小于最大重试计数（默认值 9 或用户定义的值），也会发生这种情况。

    尽管自动重试行为有助于改善大多数应用程序的复原能力和可用性，但是在执行性能基准测试时可能会造成冲突（尤其是在测量延迟时）。 如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。

* **针对小型文档进行设计以提高吞吐量**

    给定操作的请求费用（请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。 理想情况下，将你的应用程序和工作流结构设计为使你的项大小为 ~ 1KB 或类似的顺序或大小。 对于延迟敏感的应用程序，应避免出现大项-多 MB 文档将降低应用程序的速度。

## <a name="next-steps"></a>后续步骤

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
