---
title: 适用于 .NET 的 Azure Cosmos DB 性能提示
description: 了解用于提高 Azure Cosmos 数据库性能的客户端配置选项
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: eec5ab6cdf4afd63db2e77046bb19436e600ece6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720990"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>适用于 Azure Cosmos DB 和 .NET 的性能提示

> [!div class="op_single_selector"]
> * [异步 Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用一样简单。 若要了解详细信息，请参阅[如何预配容器吞吐量](how-to-provision-container-throughput.md)或[如何预配数据库吞吐量](how-to-provision-database-throughput.md)。 但是，由于 Azure Cosmos DB 是通过网络调用访问的，因此，使用 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md) 时可以通过进行客户端优化来获得最高性能。

如果有“如何改善数据库性能？”的疑问， 请考虑以下选项：

## <a name="networking"></a>联网
<a id="direct-connection"></a>

1. **连接策略：使用直接连接模式**

    客户端连接到 Azure Cosmos DB 的方式对性能有重大影响（尤其在观察到的客户端延迟方面）。 有两个密钥配置设置可用于配置客户端连接策略 – 连接“模式”和连接“协议”。  两种可用模式：

   * 网关模式
      
     网关模式在所有 SDK 平台上都受支持，并且是[Microsoft Azure](sql-api-sdk-dotnet.md)的配置默认值。 如果你的应用程序在具有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口和单个终结点。 然而，性能的折衷在于，每次读取或写入数据到 Azure Cosmos DB 时，网关模式都涉及到额外的网络跃点。 因此，直接模式因为网络跃点较少，可以提供更好的性能。 当在具有有限数量的套接字连接的环境中运行应用程序时，也建议使用网关连接模式。 

     在 Azure Functions 中使用 SDK 时（特别是在[消耗计划](../azure-functions/functions-scale.md#consumption-plan)中），请注意当前[连接的限制](../azure-functions/manage-connections.md)。 在这种情况下，如果还在 Azure Functions 应用程序中使用其他基于 HTTP 的客户端，则可能建议使用网关模式。

   * 直接模式

     直接模式通过 TCP 协议支持连接，如果使用的是[Cosmos/.Net V3 SDK](sql-api-sdk-dotnet-standard.md)，则它是默认的连接模式。

     使用网关模式时，在使用适用于 MongoDB Azure Cosmos DB 的 API 时，Cosmos DB 使用端口443和端口10250、10255和10256。 10250 端口映射到没有异地复制功能的默认 MongoDB 实例，10255/10256 端口映射到具有异地复制功能的 MongoDB 实例。 在直接模式下使用时 TCP 时，除了网关端口外，还需确保端口 10000 到 20000 范围之间的端口处于打开状态，因为 Azure Cosmos DB 使用动态 TCP 端口。 如果这些端口未处于打开状态，在尝试使用 TCP 时会收到“503 服务不可用”错误。 下表显示可用于不同 API 的连接模式以及每个 API 的服务端口用户：

     |连接模式  |支持的协议  |支持的 SDK  |API/服务端口  |
     |---------|---------|---------|---------|
     |网关  |   HTTPS    |  所有 SDK    |   SQL （443），Mongo （10250，10255，10256），表（443），Cassandra （10350），Graph （443）    |
     |直接    |     TCP    |  .NET SDK    | 10,000-20,000 范围内的端口 |

     Azure Cosmos DB 提供基于 HTTPS 的简单开放 RESTful 编程模型。 此外，它提供高效的 TCP 协议，该协议在其通信模型中也是 RESTful，可通过 .NET 客户端 SDK 获得。 TCP 协议使用 SSL 进行初始身份验证和加密通信。 为了获得最佳性能，请尽可能使用 TCP 协议。

     对于 SDK V3，在创建 CosmosClient 实例时配置连接模式，作为 CosmosClientOptions 的一部分，请记住，直接模式是默认模式。

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
     new CosmosClientOptions
     {
        ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
     });
     ```

     对于 Microsoft Azure DocumentDB SDK，连接模式是在 DocumentClient 实例与 ConnectionPolicy 参数的构造过程中配置的。 如果使用直接模式，则也可以在 ConnectionPolicy 参数中设置协议。

     ```csharp
     var serviceEndpoint = new Uri("https://contoso.documents.net");
     var authKey = "your authKey from the Azure portal";
     DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
     new ConnectionPolicy
     {
        ConnectionMode = ConnectionMode.Direct, //ConnectionMode.Gateway is the default
        ConnectionProtocol = Protocol.Tcp
     });
     ```

     由于仅在直接模式下支持 TCP，因此如果使用网关模式，则将始终使用 HTTPS 协议与网关通信，并忽略 ConnectionPolicy 中的协议值。

     ![Azure Cosmos DB 连接策略演示](./media/performance-tips/connection-policy.png)

2. **调用 OpenAsync，以避免首次请求的启动延迟**

    默认情况下，第一个请求因为必须提取地址路由表而有较高的延迟。 使用[SDK V2](sql-api-sdk-dotnet.md)时，若要避免第一次请求时的此启动延迟，应在初始化期间调用 OpenAsync （）一次，如下所示。

        await client.OpenAsync();

    > [!NOTE] 
    > OpenAsync 方法将生成请求，以获取帐户中所有容器的地址路由表。 如果帐户具有多个容器，但其应用程序访问其中的一个子集，则会产生不必要的流量，导致初始化速度变慢。 因此，在这种情况下使用 OpenAsync 方法可能会很有用，因为它会减缓应用程序启动的速度。

   <a id="same-region"></a>
3. **性能的（位于相同的 Azure 区域内）并置客户端**

    如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在与 Azure Cosmos 数据库相同的区域中。 通过大致的比较发现，在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅[ Azure Regions（Azure 区域）](https://azure.microsoft.com/regions/#services)。

    ![Azure Cosmos DB 连接策略演示](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
4. **增加线程/任务数目**

    由于对 Azure Cosmos DB 的调用是通过网络执行的，因此，可能需要改变请求的并行度，以便最大程度地减少客户端应用程序等待请求的时间。 例如，如果使用的是 .NET 的[任务并行库](https://msdn.microsoft.com//library/dd460717.aspx)，请创建大约数百个读取或写入 Azure Cosmos DB 的任务。

5. **启用加速网络**

   为了减少延迟和 CPU 抖动，建议客户端虚拟机已启用加速网络。 若要启用加速网络，请参阅[创建具有加速网络的 Windows 虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)或[创建具有加速网络的 Linux 虚拟机一](../virtual-network/create-vm-accelerated-networking-cli.md)文。


## <a name="sdk-usage"></a>SDK 用法
1. **安装最新的 SDK**

    Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 页以了解最新的 SDK 并查看改进内容。

2. **使用流 Api**

    [.NET SDK V3](sql-api-sdk-dotnet-standard.md)包含可接收和返回数据的流 api，而无需序列化。 

    不直接从 SDK 使用响应但将它们中继到其他应用程序层的中间层应用程序可以从流 Api 受益。 有关流处理的示例，请参阅[项管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement)示例。

3. **在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

    每个 DocumentClient 和 CosmosClient 实例都是线程安全的，在直接模式下操作时，它会执行高效的连接管理和地址缓存。 为了使 SDK 客户端能够实现高效的连接管理和更好的性能，建议在应用程序的生存期内使用每个 AppDomain 的单个实例。

   <a id="max-connection"></a>
4. **在使用“网关”模式时增加每台主机的 System.Net MaxConnections**

    使用“网关”模式时，Azure Cosmos DB 请求是通过 HTTPS/REST 发出的，并且受制于每个主机名或 IP 地址的默认连接限制。 可能需要将 MaxConnections 设置为较大的值 (100-1000)，以便客户端库能够同时利用多个连接来访问 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更高版本中，[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 的默认值为 50，要更改此值，可将 [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 设置为更大的值。   
5. **优化分区集合的并行查询。**

     SQL .NET SDK 版本 1.9.0 和更高版本支持并行查询，使你能够并行查询分区集合。 有关详细信息，请参阅与使用这些 SDK 相关的[代码示例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)。 并行查询旨改善查询延迟和串行配对物上的吞吐量。 并行查询提供两个参数，用户可以调整来适应自身的需求 (a) MaxDegreeOfParallelism：控制并行中运行的最大分区数 (b) MaxBufferedItemCount：控制预提取结果的数量。

    （a） ***\:*** 并行查询并行查询多个分区，从而优化并行度。 但是，来自单个分区的数据将在查询中按顺序提取。 如果将[Sdk V2](sql-api-sdk-dotnet.md)中的 `MaxDegreeOfParallelism` 或[sdk V3](sql-api-sdk-dotnet-standard.md)中的 `MaxConcurrency` 设置为分区数，则最有可能实现最高性能的查询，前提是所有其他系统条件保持不变。 如果不知道分区数，可以将并行度设置为较高的数值，系统会选择最小值（分区数、用户提供的输入）作为并行度。

    请务必注意：如果数据能均匀地分散在与查询相关的所有分区上，并行查询就能带来最大的好处。 如果对分区集合进行分区，其中全部或大部分查询所返回的数据集中于几个分区（最坏的情况下为一个分区），则这些分区将遇到查询的性能瓶颈。

    (b) 优化MaxBufferedItemCount ***并行查询专用于在客户端处理结果的当前批处理时预提取结果。\:*** 预提取帮助改进查询中的的总体延迟。 MaxBufferedItemCount 是限制预提取结果数目的参数。 将 MaxBufferedItemCount 设置为预期返回的结果数（或较大的数字）使查询从预提取获得最大的好处。

    不管并行度如何，预提取的工作方式都是相同的，并且所有分区中的数据都有一个缓冲区。  
6. **打开服务器端 GC**

    在某些情况下，降低垃圾收集的频率可能会有帮助。 在 .NET 中，应将 [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) 设置为 true。
7. **按 RetryAfter 间隔实现退让**

    在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果受到限制，客户端应用程序应按照服务器指定的重试间隔在限制时退让。 遵循退让可确保最大程度地减少等待重试的时间。 重试策略支持包含在 SQL [.NET](sql-api-sdk-dotnet.md) 和 [Java](sql-api-sdk-java.md) 的 1.8.0 和更高版本、[Node.js](sql-api-sdk-node.md) 和 [Python](sql-api-sdk-python.md) 的 1.9.0 和更高版本以及所有受支持的 [.NET Core](sql-api-sdk-dotnet-core.md) SDK 版本中。 有关详细信息，请参阅 [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)。
    
    使用 .NET SDK 1.19 版和更高版本时，存在一种机制来记录附加诊断信息和排查延迟问题，如以下示例所示。 可以记录具有较高读取延迟的请求的诊断字符串。 捕获的诊断字符串将帮助你了解观察到给定请求延迟 429 秒的次数。
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
8. **增大客户端工作负荷**

    如果以高吞吐量级别（> 50,000 RU/秒）进行测试，客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率将达到上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。
9. **缓存较低读取延迟的文档 URI**

    尽可能缓存文档 URI 以获得最佳读取性能。 创建资源时，必须定义逻辑才能缓存 resourceid。 基于 resourceid 的查找比基于名称的查找更快，因此缓存这些值可提高性能。 

   <a id="tune-page-size"></a>
10. **调整查询/读取源的页面大小以获得更好的性能**

   使用读取源功能（例如 ReadDocumentFeedAsync）执行批量文档读取，或发出 SQL 查询时，如果结果集太大，则会以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

   若要减少检索所有适用结果所需的网络往返次数，可以使用 [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 请求标头将页面大小最大增加到 1000。 在只需要显示几个结果的情况下（例如，用户界面或应用程序 API 一次只返回 10 个结果），也可以将页面大小缩小为 10，以降低读取和查询所耗用的吞吐量。

   > [!NOTE] 
   > MaxItemCount 属性不应仅用于分页目的。 它的主要用途是通过减少单个页面中返回的最大项数来改善查询性能。  

   你还可以使用可用的 Azure Cosmos DB Sdk 设置页面大小。 通过 FeedOptions 中的[MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet)属性，可以设置要在枚举操作中返回的最大项数。 将 `maxItemCount` 设置为-1 时，SDK 会自动根据文档大小查找最佳值。 例如：
    
   ```csharp
    IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
   ```
    
   执行查询时，将在 TCP 数据包中发送生成的数据。 如果为 `maxItemCount`指定太低的值，则在 TCP 数据包中发送数据所需的行程数很高，这会影响性能。 因此，如果您不确定为 `maxItemCount` 属性设置什么值，最好将其设置为-1，并让 SDK 选择默认值。 

11. **增加线程/任务数目**

    请参阅“网络”部分中的[增加线程/任务数目](#increase-threads)。

12. **使用 64 位主机进程**

    在使用 SQL .NET SDK 1.11.4 及更高版本时，SQL SDK 可以在 32 位的主机进程中运行。 但是，如果使用跨分区查询，建议使用 64 位主机进程来提高性能。 以下类型的应用程序默认为 32 位主机进程，为了将其更改为 64 位，请根据应用程序类型执行以下步骤：

    - 对于可执行应用程序，在“生成”选项卡的“项目属性”窗口中，通过取消“首选 32 位”选项可实现以上目的。

    - 对于基于 VSTest 的测试项目，可通过从“Visual Studio 测试”菜单选项中选择“测试”->“测试设置”->“默认处理器体系结构为 X64”来完成。

    - 对于本地部署的 ASP.NET Web 应用程序，可以通过在“工具” **“选项”** “项目和解决方案” **“Web 项目”下勾选“对网站和项目使用 IIS Express 的 64 位版”来完成。** ->->->

    - 对于部署在 Azure 上的 ASP.NET Web 应用程序，可以通过在 Azure 门户上的“应用程序设置”中选择“64 位平台”来完成。

## <a name="indexing-policy"></a>索引策略
 
1. **从索引中排除未使用的路径以加快写入速度**

    Cosmos DB 的索引策略还允许使用索引路径（IndexingPolicy.IncludedPaths 和 IndexingPolicy.ExcludedPaths）指定要在索引中包括或排除的文档路径。 在事先知道查询模式的方案中，使用索引路径可改善写入性能并降低索引存储空间，因为索引成本与索引的唯一路径数目直接相关。  例如，下面的代码演示如何使用 "*" 通配符从索引中排除文档的整个部分（子树）。

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    有关详细信息，请参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="throughput"></a>吞吐量
<a id="measure-rus"></a>

1. **测量和优化较低的每秒请求单位使用量**

    Azure Cosmos DB 提供一组丰富的数据库操作，包括 UDF 的关系和层次查询、存储过程和触发 – 所有都在数据库集合的文档上操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 与考虑和管理硬件资源不同的是，可以考虑将请求单位 (RU) 作为所需资源的单个措施，以执行各种数据库操作和服务应用程序请求。

    吞吐量是基于为每个容器设置的[请求单位](request-units.md)数量预配的。 请求单位消耗以每秒速率评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。 

    查询的复杂性会影响操作使用的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

    若要测量任何操作（创建、更新或删除）的开销，请检查[x-ms-请求费用](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)标头（或 .net SDK 中的 ResourceResponse\<t > 或 FeedResponse\<t > 中的等效 RequestCharge 属性）来度量这些操作占用的请求单位数。

    ```csharp
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

    在此标头中返回的请求费用是预配吞吐量的一小部分（即 2000 RU/秒）。 例如，如果上述查询返回 1000 个 1KB 文档，则操作开销为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>
2. **处理速率限制/请求速率太大**

    当客户端尝试超过帐户保留的吞吐量时，服务器的性能不会降低，并且不使用超过保留级别的吞吐量容量。 服务器将抢先结束 RequestRateTooLarge（HTTP 状态代码 429）的请求并返回 [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头，该标头指示重新尝试请求前用户必须等待的时间量（以毫秒为单位）。

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
