---
title: "适用于 .NET 的 Azure Cosmos DB 性能提示 | Microsoft Docs"
description: "了解用于提高 Azure Cosmos DB 数据库性能的客户端配置选项"
keywords: "如何提高数据库性能"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mimig
ms.openlocfilehash: 84a1913bd218d512f7f2818291f59d98628a7272
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>适用于 Azure Cosmos DB 和 .NET 的性能提示

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用或 [SDK 方法调用](set-throughput.md#set-throughput-sdk)一样简单。 但是，由于 Azure Cosmos DB 是通过网络调用访问的，因此，使用 [SQL .NET SDK](documentdb-sdk-dotnet.md) 时可以通过进行客户端优化来获得最高性能。

如果有“如何改善数据库性能？”的疑问， 请考虑以下选项：

## <a name="networking"></a>网络
<a id="direct-connection"></a>

1. **连接策略：使用直接连接模式**

    客户端连接到 Azure Cosmos DB 的方式对性能有重大影响（尤其在观察到的客户端延迟方面）。 有两个密钥配置设置可用于配置客户端连接策略 – 连接模式和连接[*协议*](#connection-protocol)。  两种可用模式：

   1. 网关模式（默认）
   2. 直接模式

      网关模式受所有 SDK 平台的支持并已配置为默认设置。  如果应用程序在有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口与单个终结点。 但是，对于性能的影响是每次读取或写入 Azure Cosmos DB 数据时，网关模式都涉及到额外的网络跃点。 因此，直接模式因为网络跃点较少，可以提供更好的性能。
<a id="use-tcp"></a>
2. **连接策略：使用 TCP 协议**

    使用直接模式时，有两个可用的协议选项：

   * TCP
   * HTTPS

     Azure Cosmos DB 提供基于 HTTPS 的简单开放 RESTful 编程模型。 此外，它提供了通信模型中有效的 RESTful TCP 协议，可通过.NET 客户端 SDK 获取。 直接 TCP 和 HTTPS 使用 SSL 进行初始身份验证和加密通信。 为了获得最佳性能，请尽可能使用 TCP 协议。

     在网关模式下使用 TCP 时，TCP 端口 443 是 Azure Cosmos DB 端口，10255 是 MongoDB API 端口。 在直接模式下使用时 TCP 时，除了网关端口外，还需确保端口 10000 到 20000 范围之间的端口处于打开状态，因为 Azure Cosmos DB 使用动态 TCP 端口。 如果这些端口未处于打开状态，在尝试使用 TCP 时会收到“503 服务不可用”错误。

     连接模式是在构造 DocumentClient 实例期间使用 ConnectionPolicy 参数配置的。 如果使用直接模式，则也可以在 ConnectionPolicy 参数中设置协议。

    ```C#
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    由于只有直接模式支持 TCP，因此如果使用网关模式，HTTPS 协议始终用来与网关通信，并忽略 ConnectionPolicy 中的 Protocol 值。

    ![Azure Cosmos DB 连接策略演示](./media/performance-tips/connection-policy.png)

3. **调用 OpenAsync，以避免首次请求的启动延迟**

    默认情况下，第一个请求因为必须提取地址路由表而有较高的延迟。 若要避免首次请求的启动延迟，则应调用 OpenAsync() 一次在初始化期间，如下所示。

        await client.OpenAsync();
   <a id="same-region"></a>
4. **性能的（位于相同的 Azure 区域内）并置客户端**

    如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在与 Azure Cosmos DB 数据库所在的相同区域中。 通过大致的比较发现，在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅[ Azure Regions（Azure 区域）](https://azure.microsoft.com/regions/#services)。

    ![Azure Cosmos DB 连接策略演示](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **增加线程/任务数目**

    由于对 Azure Cosmos DB 的调用是通过网络执行的，因此，可能需要改变请求的并行度，以便最大程度地减少客户端应用程序等待请求的时间。 例如，如果使用的是 .NET 的[任务并行库](https://msdn.microsoft.com//library/dd460717.aspx)，请创建大约数百个读取或写入 Azure Cosmos DB 的任务。

## <a name="sdk-usage"></a>SDK 用法
1. **安装最新的 SDK**

    Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 页以了解最新的 SDK 并查看改进内容。
2. **在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

    每个 DocumentClient 实例都是线程安全的，在直接模式下运行时可执行高效的连接管理和地址缓存。 若要通过 DocumentClient 获得高效的连接管理和更好的性能，建议在应用程序生存期内对每个 AppDomain 使用单个 DocumentClient 实例。

   <a id="max-connection"></a>
3. **在使用“网关”模式时增加每台主机的 System.Net MaxConnections**

    使用“网关”模式时，Azure Cosmos DB 请求是通过 HTTPS/REST 发出的，并且受制于每个主机名或 IP 地址的默认连接限制。 可能需要将 MaxConnections 设置为较大的值 (100-1000)，以便客户端库能够同时利用多个连接来访问 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更高版本中，[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 的默认值为 50，要更改此值，可将 [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 设置为更大的值。   
4. **优化分区集合的并行查询。**

     SQL .NET SDK 版本 1.9.0 和更高版本支持并行查询，使你能够并行查询分区集合（有关详细信息，请参阅[使用 SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) 以及相关的[代码示例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)）。 并行查询旨改善查询延迟和串行配对物上的吞吐量。 并行查询提供两个参数，用户可以调整来适应自身的需求 (a) MaxDegreeOfParallelism：控制并行中运行的最大分区数 (b) MaxBufferedItemCount：控制预提取结果的数量。

    (a) 优化 MaxDegreeOfParallelism\: 并行查询的方式是查询并行中的多个分区。 但对于查询，按顺序提取单独分区集合的数据。 因此，要将 MaxDegreeOfParallelism 设置为分区数，必须实现大多数性能查询的最大机会，假设所有其他系统前提条件保持不变。 如果不知道分区数，可将 MaxDegreeOfParallelism 设置为较高的数值，系统会选择最小值（分区的数量，用户输入）作为 MaxDegreeOfParallelism。

    请务必注意：如果数据能均匀地分散在与查询相关的所有分区上，并行查询就能带来最大的好处。 如果对分区集合进行分区，其中全部或大部分查询所返回的数据集中于几个分区（最坏的情况下为一个分区），则这些分区将遇到查询的性能瓶颈。

    (b) 优化MaxBufferedItemCount\: 并行查询专用于在客户端处理结果的当前批处理时预提取结果。 预提取帮助改进查询中的的总体延迟。 MaxBufferedItemCount 是限制预提取结果数目的参数。 将 MaxBufferedItemCount 设置为预期返回的结果数（或较大的数字）使查询从预提取获得最大的好处。

    预提取的工作方式不因 MaxDegreeOfParallelism 而异，并且有一个单独的缓冲区用来存储所有分区的数据。  
5. **打开服务器端 GC**

    在某些情况下，降低垃圾收集的频率可能会有帮助。 在 .NET 中，应将 [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) 设置为 true。
6. **按 RetryAfter 间隔实现退让**

    在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果受到限制，客户端应用程序应按照服务器指定的重试间隔在限制时退让。 遵循退让可确保最大程度地减少等待重试的时间。 重试策略支持包含在 SQL [.NET](sql-api-sdk-dotnet.md) 和 [Java](sql-api-sdk-java.md) 的 1.8.0 和更高版本、[Node.js](sql-api-sdk-node.md) 和 [Python](sql-api-sdk-python.md) 的 1.9.0 和更高版本以及所有受支持的 [.NET Core](sql-api-sdk-dotnet-core.md) SDK 版本中。 有关详细信息，请参阅[超过保留的吞吐量限制](request-units.md#RequestRateTooLarge)和 [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)。
7. **增大客户端工作负荷**

    如果以高吞吐量级别（> 50,000 RU/秒）进行测试，客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率将达到上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。
8. **缓存较低读取延迟的文档 URI**

    尽可能缓存文档 URI 以获得最佳读取性能。
   <a id="tune-page-size"></a>
9. **调整查询/读取源的页面大小以获得更好的性能**

    使用读取源功能（例如 ReadDocumentFeedAsync）执行批量文档读取，或发出 SQL 查询时，如果结果集太大，则会以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

    若要减少检索所有适用结果所需的网络往返次数，可以使用 [x-ms-max-item-count](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers) 请求标头将页面大小最大增加到 1000。 在只需要显示几个结果的情况下（例如，用户界面或应用程序 API 一次只返回 10 个结果），也可以将页面大小缩小为 10，以降低读取和查询所耗用的吞吐量。

    也可以使用可用的 Azure Cosmos DB SDK 设置页面大小。  例如：

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **增加线程/任务数目**

    请参阅“网络”部分中的[增加线程/任务数目](#increase-threads)。

11. **使用 64 位主机进程**

    在使用 SQL .NET SDK 1.11.4 及更高版本时，SQL SDK 可以在 32 位的主机进程中运行。 但是，如果使用跨分区查询，建议使用 64 位主机进程来提高性能。 以下类型的应用程序默认为 32 位主机进程，为了将其更改为 64 位，请根据应用程序类型执行以下步骤：

    - 对于可执行应用程序，在“生成”选项卡的“项目属性”窗口中，通过取消“首选 32 位”选项可实现以上目的。

    - 对于基于 VSTest 的测试项目，可通过从“Visual Studio 测试”菜单选项中选择“测试”->“测试设置”->“默认处理器体系结构为 X64”来完成。

    - 对于本地部署的 ASP.NET Web 应用程序，可以通过在“工具”->“选项”->“项目和解决方案”->“Web 项目”下勾选“对网站和项目使用 IIS Express 的 64 位版”来完成。

    - 对于部署在 Azure 上的 ASP.NET Web 应用程序，可以通过在 Azure 门户上的“应用程序设置”中选择“64 位平台”来完成。

## <a name="indexing-policy"></a>索引策略
 
1. **从索引中排除未使用的路径以加快写入速度**

    Cosmos DB 的索引策略还允许使用索引路径（IndexingPolicy.IncludedPaths 和 IndexingPolicy.ExcludedPaths）指定要在索引中包括或排除的文档路径。 在事先知道查询模式的方案中，使用索引路径可改善写入性能并降低索引存储空间，因为索引成本与索引的唯一路径数目直接相关。  例如，以下代码演示了如何使用“*”通配符 从索引中排除文档的整个部分（也称为子树）。

    ```C#
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    有关详细信息，请参阅 [Azure Cosmos DB 索引策略](indexing-policies.md)。

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **测量和优化较低的每秒请求单位使用量**

    Azure Cosmos DB 提供一组丰富的数据库操作，包括 UDF 的关系和层次查询，存储过程和触发器 – 所有这些都是对数据库集合内的文档进行的操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 与考虑和管理硬件资源不同的是，可以考虑将请求单位 (RU) 作为所需资源的单个措施，以执行各种数据库操作和服务应用程序请求。

    吞吐量是基于为每个容器设置的[请求单位](request-units.md)数量预配的。 请求单位消耗以每秒速率评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。 

    查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

    若要测量任何操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) 标头（或 .NET SDK 中 ResourceResponse<T> 或 FeedResponse<T> 中等效的 RequestCharge 属性）来测量这些操作占用的请求单位数。

    ```C#
    // Measure the performance (request units) of writes
    ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
    Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
    // Measure the performance (request units) of queries
    IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
    while (queryable.HasMoreResults)
         {
              FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
              Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
         }
    ```             

    在此标头中返回的请求费用是预配吞吐量的一小部分（即 2000 RU/秒）。 例如，如果上述查询返回 1000 个 1KB 文档，则操作开销为 1000。 因此在一秒内，服务器在限制后续请求之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>
2. **处理速率限制/请求速率太大**

    当客户端尝试超过帐户保留的吞吐量时，服务器的性能不会降低，并且不使用超过保留级别的吞吐量容量。 服务器将抢先结束 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) 标头，该标头指示重新尝试请求前用户必须等待的时间量（以毫秒为单位）。

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

    如果存在多个高于请求速率的请求操作，则客户端当前在内部设置为 9 的默认重试计数可能无法满足需要；在此情况下，客户端就会向应用程序引发 DocumentClientException，其状态代码为 429。 可以通过在 ConnectionPolicy 实例上设置 RetryOptions 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则在 30 秒的累积等待时间后将返回 DocumentClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（默认值 9 或用户定义的值），也会发生这种情况。

    尽管自动重试行为有助于改善大多数应用程序的复原能力和可用性，但是在执行性能基准测试时可能会造成冲突（尤其是在测量延迟时）。  如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。
3. **针对小型文档进行设计以提高吞吐量**

    给定操作的请求费用（即请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。

## <a name="next-steps"></a>后续步骤
有关用于评估 Azure Cosmos DB 以在少量客户端计算机上实现高性能的示例应用程序，请参阅[执行 Azure Cosmos DB 缩放和性能测试](performance-testing.md)。

此外，若要了解如何设计应用程序以实现缩放和高性能的详细信息，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
