---
title: Azure Cosmos DB Async Java SDK v2 的性能提示
description: 了解用于提高 Async Java SDK v2 的 Azure Cosmos 数据库性能的客户端配置选项
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 1a3ec22b9d1375f1c438d24791389284c1d4ee84
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982541"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Azure Cosmos DB Async Java SDK v2 的性能提示

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> 这*不*是最新的 Java SDK for Azure Cosmos DB！ 请考虑将 Azure Cosmos DB Java SDK v4 用于项目。 若要升级，请按照[迁移到 Azure Cosmos DB JAVA SDK v4](migrate-java-v4-sdk.md)指南和[反应器 vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)指南中的说明进行操作。 
> 
> 本文中的性能提示仅适用于 Azure Cosmos DB Async Java SDK v2。 有关详细信息，请参阅 Azure Cosmos DB Async Java SDK v2[发行说明](sql-api-sdk-async-java.md)、 [Maven 存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)和 Azure Cosmos DB Async java sdk v2[疑难解答指南](troubleshoot-java-async-sdk.md)。
>

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用或 SDK 方法调用一样简单。 但是，由于通过网络调用访问 Azure Cosmos DB，因此，在使用[Azure Cosmos DB Async JAVA SDK v2](sql-api-sdk-async-java.md)时，可以通过客户端优化来实现最高性能。

如果有“如何改善数据库性能？”的疑问， 请考虑以下选项：

## <a name="networking"></a>网络

* **连接模式：使用直接模式**
<a id="direct-connection"></a>
    
    客户端连接到 Azure Cosmos DB 的方式对性能有重大影响（尤其在客户端延迟方面）。 ConnectionMode 是可用于配置客户端 ConnectionPolicy 的关键配置设置   。 对于 Azure Cosmos DB Async Java SDK v2，两个可用的 Connectionmode 是：  
      
    * [网关（默认值）](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [直接](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    网关模式受所有 SDK 平台支持，默认情况下，它是已配置的选项。 如果应用程序在有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口与单个终结点。 但是，对于性能的影响是每次在 Azure Cosmos DB 中读取或写入数据时，网关模式都涉及到额外的网络跃点。 因此，由于涉及的网络跃点较少，直接模式会提供更好的性能。

    ConnectionMode 是在构造 DocumentClient 实例期间使用 ConnectionPolicy 参数配置的    。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK V2 （Maven：： azure-cosmosdb）

    ```java
        public ConnectionPolicy getConnectionPolicy() {
          ConnectionPolicy policy = new ConnectionPolicy();
          policy.setConnectionMode(ConnectionMode.Direct);
          policy.setMaxPoolSize(1000);
          return policy;
        }

        ConnectionPolicy connectionPolicy = new ConnectionPolicy();
        DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

* **将客户端并置在同一 Azure 区域内以提高性能** <a id="same-region"></a>

    如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在与 Azure Cosmos 数据库所在的相同区域中。 通过大致的比较发现，在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅 [Azure Regions](https://azure.microsoft.com/regions/#services)（Azure 区域）。

    ![Azure Cosmos DB 连接策略演示](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK 用法
* **安装最新的 SDK**

    Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 Azure Cosmos DB Async Java SDK v2[发行说明](sql-api-sdk-async-java.md)页，以确定最新的 SDK 并查看改进。

* **在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

    每个 AsyncDocumentClient 实例都是线程安全的，可执行高效的连接管理和地址缓存。 若要通过 AsyncDocumentClient 获得高效的连接管理和更好的性能，建议在应用程序生存期内对每个 AppDomain 使用单个 AsyncDocumentClient 实例。

   <a id="max-connection"></a>

* **优化 ConnectionPolicy**

    默认情况下，使用 Azure Cosmos DB Async Java SDK v2 时，直接模式 Cosmos DB 通过 TCP 进行请求。 在内部，SDK 使用特殊的直接模式体系结构来动态管理网络资源并获得最佳性能。

    在 Azure Cosmos DB Async Java SDK v2 中，直接模式是在大多数工作负载下提高数据库性能的最佳选择。 

    * ***直接模式概述***

        ![直接模式体系结构插图](./media/performance-tips-async-java/rntbdtransportclient.png)

        在直接模式下采用的客户端体系结构使得网络利用率可预测，并实现对 Azure Cosmos DB 副本的多路访问。 上图显示了直接模式如何将客户端请求路由到 Cosmos DB 后端中的副本。 直接模式体系结构在客户端上为每个数据库副本最多分配 10 个通道  。 一个通道是前面带有请求缓冲区（深度为 30 个请求）的 TCP 连接。 属于某个副本的通道由该副本的服务终结点按需动态分配  。 当用户在直接模式下发出请求时，TransportClient 会根据分区键将请求路由到适当的服务终结点  。 请求队列在服务终结点之前缓冲请求  。

    * ***直接模式的 ConnectionPolicy 配置选项***

        第一步是使用下面推荐的配置设置。 如果遇到有关此特定主题方面的问题，请与 [Azure Cosmos DB 团队](mailto:CosmosDBPerformanceSupport@service.microsoft.com)联系。

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

    * ***直接模式的编程提示***

        查看 Azure Cosmos DB Async Java SDK v2[疑难解答](troubleshoot-java-async-sdk.md)文章作为解决任何 SDK 问题的基线。

        使用直接模式时的一些重要编程提示：

        + **在应用程序中使用多线程以高效进行 TCP 数据传输** - 发出请求后，应用程序应订阅接收另一线程上的数据。 否则就会强制执行非预期的“半双工”操作，后续请求会被阻止并等待上一个请求的回复。

        + **在专用线程上执行计算密集型工作负荷** - 出于类似于上一提示的原因，最好是将复杂数据处理等操作放置在单独的线程中。 从另一数据存储提取数据的请求（例如，如果线程同时利用 Azure Cosmos DB 和 Spark 数据存储）可能会遇到延迟增大的情况，建议生成一个等待其他数据存储做出响应的附加线程。

            + Azure Cosmos DB Async Java SDK v2 中的基础网络 IO 由 Netty 管理，请参阅这些[提示以避免阻止 NETTY IO 线程的编码模式](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread)。

        + **数据建模** - Azure Cosmos DB SLA 假定文档大小小于 1KB。 优化数据模型和编程以优先使用较小的文档大小，往往可以减小延迟。 如果需要存储和检索大于 1KB 的文档，建议的方法是在文档中链接到 Azure Blob 存储中的数据。


* **优化分区集合的并行查询。**

    Azure Cosmos DB Async Java SDK v2 支持并行查询，这使你能够并行查询分区集合。 有关详细信息，请参阅与使用这些 SDK 相关的[代码示例](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples)。 并行查询旨改善查询延迟和串行配对物上的吞吐量。

    * ***优化 setMaxDegreeOfParallelism\:***
    
        并行查询的工作原理是并行查询多个分区。 但就查询本身而言，会按顺序提取单个已分区集合中的数据。 因此，通过使用 setMaxDegreeOfParallelism 设置分区数，最有可能实现查询的最高性能，但前提是所有其他系统条件仍保持不变。 如果不知道分区数，可使用 setMaxDegreeOfParallelism 设置一个较高的数值，系统会选择最小值（分区数、用户输入）作为最大并行度。

        必须注意，如果查询时数据均衡分布在所有分区之间，则并行查询可提供最大的优势。 如果对分区集合进行分区，其中全部或大部分查询所返回的数据集中于几个分区（最坏的情况下为一个分区），则这些分区会遇到查询的性能瓶颈。

    * ***优化 setMaxBufferedItemCount\:***
    
        并行查询设计为当客户端正在处理当前结果批时预提取结果。 预提取帮助改进查询中的的总体延迟。 setMaxBufferedItemCount 会限制预提取结果的数目。 通过将 setMaxBufferedItemCount 设置为预期返回的结果数（或较高的数值），可使查询从预提取获得最大的好处。

        预提取的工作方式不因 MaxDegreeOfParallelism 而异，并且有一个单独的缓冲区用来存储所有分区的数据。

* **按 getRetryAfterInMilliseconds 间隔实现回退**

    在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果受到限制，客户端应用程序应按照服务器指定的重试间隔退让。 遵循退让可确保最大程度地减少等待重试的时间。

* **增大客户端工作负荷**

    如果以高吞吐量级别（> 50,000 RU/s）进行测试，客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率将达到上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

* **使用基于名称的寻址**

    使用基于名称的寻址，其中的链接格式为 `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`，而不是使用格式为 `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` 的 SelfLinks (\_self)（旨在避免检索用于构造链接的所有资源的 ResourceId）。 此外，由于会重新创建这些资源（名称可能相同），因此，缓存这些资源的用处不大。

   <a id="tune-page-size"></a>

* **调整查询/读取源的页面大小以获得更好的性能**

    使用读取源功能（例如 readDocuments）执行批量文档读取时，或发出 SQL 查询时，如果结果集太大，则以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

    若要减少检索所有适用结果所需的网络往返次数，可以使用 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 请求标头将页面大小最大增加到 1000。 在只需要显示几个结果的情况下（例如，用户界面或应用程序 API 一次只返回 10 个结果），也可以将页面大小缩小为 10，以降低读取和查询所耗用的吞吐量。

    也可以使用 setMaxItemCount 方法设置页面大小。

* **使用相应的计划程序（避免窃取事件循环 IO Netty 线程）**

    Azure Cosmos DB Async Java SDK v2 将[netty](https://netty.io/)用于非阻止 IO。 SDK 使用固定数量的 IO netty 事件循环线程（数量与计算机提供的 CPU 核心数相同）来执行 IO 操作。 API 返回的可观测对象针对某个共享的 IO 事件循环 netty 线程发出结果。 因此，切勿阻塞共享的 IO 事件循环 netty 线程。 针对 IO 事件循环 netty 线程执行 CPU 密集型工作或者阻塞操作可能导致死锁，或大大减少 SDK 吞吐量。

    例如，以下代码针对事件循环 IO netty 线程执行 CPU 密集型工作：

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-noscheduler"></a>Async Java SDK V2 （Maven：： azure-cosmosdb）

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    收到结果后，如果想要针对结果执行 CPU 密集型工作，应避免针对事件循环 IO netty 线程执行。 可以提供自己的计划程序，以提供自己的线程来运行工作。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-scheduler"></a>Async Java SDK V2 （Maven：： azure-cosmosdb）

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    根据工作的类型，应该使用相应的现有 RxJava 计划程序来执行工作。 请阅读 [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)。

    有关详细信息，请查看 Azure Cosmos DB Async Java SDK v2 的[GitHub 页面](https://github.com/Azure/azure-cosmosdb-java)。

* **禁用 netty 的日志记录**

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

* **使用 netty 的本机 TLS/SSL 实现**

    Netty 可以直接对 TLS 实现堆栈使用 OpenSSL，以获得更好的性能。 如果没有此配置，netty 将回退到 Java 的默认 TLS 实现。

    在 Ubuntu 上：
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    并将以下依赖项添加到项目的 maven 依赖项：
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

对于其他平台（Red Hat、Windows、Mac 等），请参考 https://netty.io/wiki/forked-tomcat-native.html 中的说明

## <a name="indexing-policy"></a>索引策略
 
* **从索引中排除未使用的路径以加快写入速度**

    Azure Cosmos DB 的索引策略允许使用索引路径（setIncludedPaths 和 setExcludedPaths）指定要在索引中包括或排除的文档路径。 在事先知道查询模式的方案中，使用索引路径可改善写入性能并降低索引存储空间，因为索引成本与索引的唯一路径数目直接相关。 例如，以下代码演示如何使用“*”通配符从索引编制中排除文档的整个部分（也称为子树）。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK V2 （Maven：： azure-cosmosdb）

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    有关索引的详细信息，请参阅 [Azure Cosmos DB 索引策略](indexing-policies.md)。

## <a name="throughput"></a>吞吐量
<a id="measure-rus"></a>

* **测量和优化较低的每秒请求单位使用量**

    Azure Cosmos DB 提供一组丰富的数据库操作，包括 UDF 的关系和层次查询、存储过程和触发 – 所有都在数据库集合的文档上操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 与考虑和管理硬件资源不同的是，可以考虑将请求单位 (RU) 作为所需资源的单个措施，以执行各种数据库操作和服务应用程序请求。

    吞吐量是基于为每个容器设置的[请求单位](request-units.md)数量预配的。 请求单位消耗以每秒速率评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。

    查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

    若要测量任何操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 标头来测量这些操作占用的请求单位数。 还可以查看 ResourceResponse\<T> 或 FeedResponse\<T> 中等效的 RequestCharge 属性。

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK V2 （Maven：： azure-cosmosdb）

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    在此标头中返回的请求费用是预配吞吐量的一小部分。 例如，如果预配了 2000 RU/s，上述查询返回 1000 个 1KB 文档，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。

<a id="429"></a>
* **处理速率限制/请求速率太大**

    客户端尝试超过帐户保留的吞吐量时，服务器的性能不会降低，并且不会使用超过保留级别的吞吐量容量。 服务器将抢先结束 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 标头，该标头指示重新尝试请求前用户必须等待的时间量（以毫秒为单位）。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

    如果存在多个高于请求速率的请求操作，则客户端当前在内部设置为 9 的默认重试计数可能无法满足需要；在此情况下，客户端就会向应用程序引发 DocumentClientException，其状态代码为 429。 可以通过在 ConnectionPolicy 实例上使用 setRetryOptions 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则在 30 秒的累积等待时间后返回 DocumentClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（默认值 9 或用户定义的值），也会发生这种情况。

    尽管自动重试行为有助于改善大多数应用程序的复原能力和可用性，但是在执行性能基准测试时可能会造成冲突（尤其是在测量延迟时）。 如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。

* **针对小型文档进行设计以提高吞吐量**

    给定操作的请求费用（请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。

## <a name="next-steps"></a>后续步骤

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
