---
title: Azure Cosmos DB 同步 Java SDK v2 的性能提示
description: 了解用于提高同步 Java SDK v2 的 Azure Cosmos 数据库性能的客户端配置选项
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 9475fce054356606c09947721019a264143a716b
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982507"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Azure Cosmos DB 同步 Java SDK v2 的性能提示

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [同步 Java SDK v2](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> 这*不*是最新的 Java SDK for Azure Cosmos DB！ 请考虑将 Azure Cosmos DB Java SDK v4 用于项目。 若要升级，请按照[迁移到 Azure Cosmos DB JAVA SDK v4](migrate-java-v4-sdk.md)指南和[反应器 vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)指南中的说明进行操作。 
> 
> 这些性能提示仅适用于 Azure Cosmos DB Sync Java SDK v2。 有关详细信息，请查看 Azure Cosmos DB 同步 Java SDK v2[发行说明](sql-api-sdk-java.md)和[Maven 存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)。
>

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用一样简单。 若要了解详细信息，请参阅[如何预配容器吞吐量](how-to-provision-container-throughput.md)或[如何预配数据库吞吐量](how-to-provision-database-throughput.md)。 但是，由于通过网络调用访问 Azure Cosmos DB，因此，在使用[Azure Cosmos DB Sync JAVA SDK v2](documentdb-sdk-java.md)时，可以通过客户端优化来实现最高性能。

如果有“如何改善数据库性能？”的疑问， 请考虑以下选项：

## <a name="networking"></a>网络
<a id="direct-connection"></a>

1. **连接模式：使用 DirectHttps**

    客户端连接到 Azure Cosmos DB 的方式对性能有重大影响（尤其在观察到的客户端延迟方面）。 有一个密钥配置设置可用于配置客户端[ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)。  有两种可用 ConnectionMode：

   1. [网关（默认值）](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      网关模式受所有 SDK 平台的支持并已配置为默认设置。  如果应用程序在有严格防火墙限制的企业网络中运行，则网关是最佳选择，因为它使用标准 HTTPS 端口与单个终结点。 但是，对于性能的影响是每次在 Azure Cosmos DB 中读取或写入数据时，网关模式都涉及到额外的网络跃点。 因此，DirectHttps 模式因为网络跃点较少，可以提供更好的性能。 

      Azure Cosmos DB 同步 Java SDK v2 使用 HTTPS 作为传输协议。 HTTPS 使用 TLS 进行初始身份验证和加密通信。 使用 Azure Cosmos DB 同步 Java SDK v2 时，只需打开 HTTPS 端口443。 

      ConnectionMode 是在构造 DocumentClient 实例期间使用 ConnectionPolicy 参数配置的。 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>同步 Java SDK V2 （Maven：： azure）

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      ![Azure Cosmos DB 连接策略演示](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **将客户端并置在同一 Azure 区域中以提高性能**

    如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在与 Azure Cosmos 数据库所在的相同区域中。 通过大致的比较发现，在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅 [Azure Regions](https://azure.microsoft.com/regions/#services)（Azure 区域）。

    ![Azure Cosmos DB 连接策略演示](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK 用法
1. **安装最新的 SDK**

    Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](documentdb-sdk-java.md) 页以了解最新的 SDK 并查看改进内容。
2. **在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

    每个 [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) 实例都是线程安全的，在直接模式下运行时可执行高效的连接管理和地址缓存。 若要通过 DocumentClient 获得高效的连接管理和更好的性能，建议在应用程序生存期内对每个 AppDomain 使用单个 DocumentClient 实例。

   <a id="max-connection"></a>
3. **使用网关模式时，增加每个主机的 MaxPoolSize**

    使用“网关”模式时，Azure Cosmos DB 请求是通过 HTTPS/REST 发出的，并且受制于每个主机名或 IP 地址的默认连接限制。 可能需要将 MaxPoolSize 设置为较大的值 (200-1000)，以便客户端库能够同时利用多个连接来访问 Azure Cosmos DB。 在 Azure Cosmos DB 同步 Java SDK v2 中， [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize)的默认值为100。 使用 [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) 可更改该值。

4. **优化分区集合的并行查询。**

    Azure Cosmos DB 同步 Java SDK 版本1.9.0 和更高版本支持并行查询，这使您能够并行查询分区集合。 有关详细信息，请参阅与使用这些 SDK 相关的[代码示例](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples)。 并行查询旨改善查询延迟和串行配对物上的吞吐量。

    (a) ***优化 setMaxDegreeOfParallelism\:*** 并行查询的方式是并行查询多个分区。 但就查询本身而言，会按顺序提取单个已分区集合中的数据。 因此，通过使用 [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) 设置分区数，最有可能实现查询的最高性能，但前提是所有其他系统条件仍保持不变。 如果不知道分区数，可使用 setMaxDegreeOfParallelism 设置一个较高的数值，系统会选择最小值（分区数、用户输入）作为最大并行度。 

    必须注意，如果查询时数据均衡分布在所有分区之间，则并行查询可提供最大的优势。 如果对分区集合进行分区，其中全部或大部分查询所返回的数据集中于几个分区（最坏的情况下为一个分区），则这些分区会遇到查询的性能瓶颈。

    (b) ***优化 setMaxBufferedItemCount\:*** 并行查询专用于在客户端处理当前一批结果时预提取结果。 预提取帮助改进查询中的的总体延迟。 setMaxBufferedItemCount 会限制预提取结果的数目。 通过将 [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) 设置为预期返回的结果数（或较高的数值），可使查询从预提取获得最大的好处。

    预提取的工作方式不因 MaxDegreeOfParallelism 而异，并且有一个单独的缓冲区用来存储所有分区的数据。  

5. **按 getRetryAfterInMilliseconds 间隔实现回退**

    在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果受到限制，客户端应用程序应按照服务器指定的重试间隔在限制时退让。 遵循退让可确保最大程度地减少等待重试的时间。 重试策略支持包含在[Azure Cosmos DB Sync JAVA SDK](documentdb-sdk-java.md)的版本1.8.0 和更高版本中。 有关详细信息，请参阅 [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds)。

6. **增大客户端工作负荷**

    如果以高吞吐量级别（> 50,000 RU/s）进行测试，客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率将达到上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

7. **使用基于名称的寻址**

    使用基于名称的寻址，其中的链接格式为 `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`，而不是使用格式为 `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` 的 SelfLinks (\_self)（旨在避免检索用于构造链接的所有资源的 ResourceId）。 此外，由于会重新创建这些资源（名称可能相同），因此，缓存这些资源的用处不大。

   <a id="tune-page-size"></a>
8. **调整查询/读取源的页面大小以获得更好的性能**

    使用读取源功能（例如 [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)）执行批量文档读取时，或发出 SQL 查询时，如果结果集太大，则以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

    若要减少检索所有适用结果所需的网络往返次数，可以使用 [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 请求标头将页面大小最大增加到 1000。 在只需要显示几个结果的情况下（例如，用户界面或应用程序 API 一次只返回 10 个结果），也可以将页面大小缩小为 10，以降低读取和查询所耗用的吞吐量。

    也可以使用 [setPageSize 方法](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)设置页面大小。

## <a name="indexing-policy"></a>索引策略
 
1. **从索引中排除未使用的路径以加快写入速度**

    Azure Cosmos DB 的索引策略允许使用索引路径（[setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths)和[setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)）指定要在索引中包括或排除的文档路径。 在事先知道查询模式的方案中，使用索引路径可改善写入性能并降低索引存储空间，因为索引成本与索引的唯一路径数目直接相关。  例如，下面的代码演示如何使用 "*" 通配符从索引中排除文档的整个部分（子树）。


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>同步 Java SDK V2 （Maven：： azure）

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

1. **测量和优化较低的每秒请求单位使用量**

    Azure Cosmos DB 提供一组丰富的数据库操作，包括 UDF 的关系和层次查询、存储过程和触发 – 所有都在数据库集合的文档上操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 与考虑和管理硬件资源不同的是，可以考虑将请求单位 (RU) 作为所需资源的单个措施，以执行各种数据库操作和服务应用程序请求。

    吞吐量是基于为每个容器设置的[请求单位](request-units.md)数量预配的。 请求单位消耗以每秒速率评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。 

    查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

    若要度量任何操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头（或 [ResourceResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) 或 [FeedResponse\<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) 中的等效 RequestCharge 属性）来度量这些操作占用的请求单位数。


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>同步 Java SDK V2 （Maven：： azure）

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    在此标头中返回的请求费用是预配吞吐量的一小部分。 例如，如果预配了 2000 RU/s，上述查询返回 1000 个 1KB 文档，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。
   <a id="429"></a>
1. **处理速率限制/请求速率太大**

    客户端尝试超过帐户保留的吞吐量时，服务器的性能不会降低，并且不会使用超过保留级别的吞吐量容量。 服务器将抢先结束 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头，该标头指示重新尝试请求前用户必须等待的时间量（以毫秒为单位）。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

    如果多个客户端一直以高于请求速率的方式累积运行，则客户端当前在内部设置为 9 的默认重试计数可能无法满足需要；在此情况下，客户端就会向应用程序引发 [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception)，其状态代码为 429。 可以通过在 [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) 实例上使用 [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则在 30 秒的累积等待时间后返回 DocumentClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（默认值 9 或用户定义的值），也会发生这种情况。

    尽管自动重试行为有助于改善大多数应用程序的复原能力和可用性，但是在执行性能基准测试时可能会造成冲突（尤其是在测量延迟时）。  如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。
3. **针对小型文档进行设计以提高吞吐量**

    给定操作的请求费用（请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。

## <a name="next-steps"></a>后续步骤
若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
