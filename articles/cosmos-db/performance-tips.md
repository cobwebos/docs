---
title: 适用于 .NET 的 Azure Cosmos DB 性能提示
description: 了解用于提高 Azure Cosmos DB 性能的客户端配置选项。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: ee2743af2f8499aec04d8b6b733e1ba4c2a82083
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546080"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>适用于 Azure Cosmos DB 和 .NET 的性能提示

> [!div class="op_single_selector"]
> * [异步 Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 无需进行重大体系结构更改或编写复杂的代码，就可以使用 Azure Cosmos DB 来缩放数据库。 扩展和缩减操作就像执行单个 API 调用一样简单。 若要了解详细信息，请参阅[如何预配容器吞吐量](how-to-provision-container-throughput.md)或[如何预配数据库吞吐量](how-to-provision-database-throughput.md)。 但由于 Azure Cosmos DB 是通过网络调用访问的，因此在使用[SQL .NET SDK](sql-api-sdk-dotnet-standard.md)时，可以通过客户端优化来实现最高性能。

因此，如果您要尝试提高数据库性能，请考虑以下选项：

## <a name="hosting-recommendations"></a>宿主建议

**对于查询密集型工作负荷，请使用 Windows 64 位而不是 Linux 或 Windows 32 位主机处理**

建议 Windows 64 位主机处理以提高性能。 SQL SDK 包含一个本机 Microsoft.azure.documents.serviceinterop.dll，用于在本地分析和优化查询。 仅在 Windows x64 平台上支持 Microsoft.azure.documents.serviceinterop.dll。 对于 Linux 和不支持 Microsoft.azure.documents.serviceinterop.dll 的其他不受支持的平台，会对网关进行额外的网络调用以获取优化的查询。 默认情况下，以下类型的应用程序使用32位主机处理。 若要将主机处理更改为64位处理，请根据应用程序的类型执行以下步骤：

- 对于可执行应用程序，可以通过在 "**生成**" 选项卡上的 "**项目属性**" 窗口中将 "[平台目标](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019)" 设置为 " **x64** " 来更改主机处理。

- 对于基于 vstest.console.exe 的测试项目，可以通过在 Visual Studio 的 "**测试**" 菜单上选择 "**测试** > " "测试**设置** > " "**默认处理器体系结构为 X64** " 来更改主机处理。

- 对于本地部署的 ASP.NET web 应用程序，你可以通过在 "**工具** > " "**选项** > " "**项目和解决方案** > " "**web 项目**" 下选择 **"使用网站和项目的64位版本 IIS Express**来更改主机处理。

- 对于部署在 Azure 上的 ASP.NET web 应用程序，可以通过在 "Azure 门户中的"**应用程序设置**"中选择**64 位**平台来更改主机处理。

> [!NOTE] 
> 默认情况下，新的 Visual Studio 项目设置为 "**任何 CPU**"。 建议将项目设置为**x64** ，使其不会切换到**x86**。 如果添加了仅限 x86 的依赖项，则设置为 "**任何 CPU** " 的项目可以轻松切换到**x86** 。<br/>
> Microsoft.azure.documents.serviceinterop.dll 需要位于执行 SDK DLL 的文件夹中。 只有手动复制 Dll 或具有自定义生成/部署系统时，才需要考虑这一点。
    
**启用服务器端垃圾回收（GC）**

在某些情况下，降低垃圾收集的频率可能会有所帮助。 在 .NET 中， [gcServer](https://msdn.microsoft.com/library/ms229357.aspx)将 r `true`设置为。

**横向扩展客户端工作负荷**

如果测试的是高吞吐量级别（超过 50000 RU/s），则客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率超出了上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

> [!NOTE] 
> 高 CPU 使用率可能导致延迟增加和请求超时异常。

## <a name="networking"></a>网络
<a id="direct-connection"></a>

**连接策略：使用直接连接模式**

客户端连接到 Azure Cosmos DB 对性能有重大影响，特别是对于观察到的客户端延迟。 有两个密钥配置设置可用于配置客户端连接策略：连接*模式*和连接*协议*。  两种可用模式：

   * 网关模式
      
     网关模式在所有 SDK 平台上都受支持，并且是[Microsoft Azure](sql-api-sdk-dotnet.md)的配置默认值。 如果你的应用程序在具有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口和单个终结点。 然而，性能的折衷在于，每次从 Azure Cosmos DB 读取数据或向其写入数据时，网关模式都涉及到额外的网络跃点。 因为网络跃点较少，所以直接模式提供更好的性能。 当你在具有有限数量的套接字连接的环境中运行应用程序时，我们还建议使用网关连接模式。

     在 Azure Functions 中使用 SDK 时（特别是在[消耗计划](../azure-functions/functions-scale.md#consumption-plan)中），请注意当前的[连接限制](../azure-functions/manage-connections.md)。 在这种情况下，如果你同时使用 Azure Functions 应用程序中的其他基于 HTTP 的客户端，则网关模式可能更好。

   * 直接模式

     直接模式支持通过 TCP 协议建立连接，如果使用的是[Cosmos/.Net V3 SDK](sql-api-sdk-dotnet-standard.md)，则这是默认的连接模式。

在网关模式下，使用适用于 MongoDB 的 Azure Cosmos DB API 时，Azure Cosmos DB 使用端口443和端口10250、10255和10256。 端口10250映射到无地域复制的默认 MongoDB 实例。 端口10255和10256映射到具有异地复制的 MongoDB 实例。
     
在直接模式下使用 TCP 时，除了网关端口外，还需确保端口范围为10000到20000，因为 Azure Cosmos DB 使用动态 TCP 端口。 如果这些端口未打开，而你尝试使用 TCP，则会收到503服务不可用错误。 此表显示了可用于各种 Api 的连接模式以及用于每个 API 的服务端口：

|连接模式  |支持的协议  |支持的 SDK  |API/服务端口  |
|---------|---------|---------|---------|
|网关  |   HTTPS    |  所有 Sdk    |   SQL （443）、MongoDB （10250、10255、10256）、Table （443）、Cassandra （10350）、Graph （443）    |
|直接    |     TCP    |  .NET SDK    | 10000到20000范围内的端口 |

Azure Cosmos DB 提供通过 HTTPS 的简单开放 RESTful 编程模型。 此外，它提供高效的 TCP 协议，该协议在其通信模型中也是 RESTful，可通过 .NET 客户端 SDK 获得。 TCP 协议使用 TLS 进行初始身份验证和加密通信。 为了获得最佳性能，请尽可能使用 TCP 协议。

对于 SDK V3，你可以在中`CosmosClient` `CosmosClientOptions`创建实例时配置连接模式。 请记住，直接模式是默认值。

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

对于 Microsoft Azure DocumentDB SDK，你可以通过使用`DocumentClient` `ConnectionPolicy`参数在构造实例期间配置连接模式。 如果使用直接模式，则还可以使用`Protocol` `ConnectionPolicy`参数设置。

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

由于只有直接模式支持 TCP，因此如果使用网关模式，则将始终使用 HTTPS 协议与网关通信，而中`Protocol` `ConnectionPolicy`的值将被忽略。

![Azure Cosmos DB 连接策略](./media/performance-tips/connection-policy.png)

**调用 OpenAsync，以避免首次请求的启动延迟**

默认情况下，第一个请求的延迟较高，因为它需要提取地址路由表。 使用[SDK V2](sql-api-sdk-dotnet.md)时，请在`OpenAsync()`初始化过程中调用一次，以避免第一次请求时的此启动延迟：

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`将生成请求以便为帐户中的所有容器获取地址路由表。 对于具有多个容器但其应用程序访问其中一个子集的帐户`OpenAsync` ，会生成不必要的流量，这会使初始化速度变慢。 因此， `OpenAsync`在这种情况下使用可能不起作用，因为这样会减缓应用程序启动的速度。

   <a id="same-region"></a>
**对于性能，在同一 Azure 区域中归置客户端**

如果可能，请将调用 Azure Cosmos DB 的任何应用程序放在与 Azure Cosmos DB 数据库相同的区域中。 下面是大致比较：在同一区域中对 Azure Cosmos DB 的调用在1毫秒到2毫秒内完成，但美国西部和东海岸之间的延迟大于 50 ms。 此延迟可能与请求不同，具体取决于请求从客户端传递到 Azure 数据中心边界时所采用的路由。 通过确保调用应用程序位于与预配的 Azure Cosmos DB 终结点相同的 Azure 区域中，可以获得尽可能低的延迟。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/regions/#services)。

![Azure Cosmos DB 连接策略](./media/performance-tips/same-region.png)<a id="increase-threads"></a>

**增加线程/任务的数目**

由于对 Azure Cosmos DB 的调用是通过网络进行的，因此，可能需要改变请求的并行度，以便客户端应用程序在请求之间等待的时间最短。 例如，如果你使用的是 .NET[任务并行库](https://msdn.microsoft.com//library/dd460717.aspx)，请按照数百个任务的顺序创建，这些任务将从或写入 Azure Cosmos DB。

**启用加速网络**
 
 为了减少延迟和 CPU 抖动，建议你在客户端虚拟机上启用加速网络。 请参阅[创建具有加速网络的 Windows 虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)或[创建具有加速网络的 Linux 虚拟机](../virtual-network/create-vm-accelerated-networking-cli.md)。

## <a name="sdk-usage"></a>SDK 用法
**安装最新的 SDK**

Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 页以了解最新的 SDK 并查看改进内容。

**使用流 Api**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md)包含可接收和返回数据的流 api，而无需序列化。 

不直接从 SDK 使用响应但将它们中继到其他应用程序层的中间层应用程序可以受益于流 Api。 有关流处理的示例，请参阅[项管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement)示例。

**在应用程序的生存期内使用单一实例 Azure Cosmos DB 客户端**

每`DocumentClient`个`CosmosClient`和实例都是线程安全的，在直接模式下操作时，它会执行高效的连接管理和地址缓存。 为了实现有效的连接管理和更好`AppDomain`的 SDK 客户端性能，我们建议你在应用程序的生存期内使用单个实例。

   <a id="max-connection"></a>

**使用网关模式时增加每台主机的 System.Net MaxConnections**

使用网关模式时，Azure Cosmos DB 通过 HTTPS/REST 发出的请求。 每个主机名或 IP 地址的默认连接限制都受到限制。 你可能需要将设置`MaxConnections`为更高的值（100到1000），以便客户端库可以同时使用多个连接来 Azure Cosmos DB。 在 .NET SDK 1.8.0 和更高版本中， [servicepointmanager.defaultconnectionlimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx)的默认值为50。 若要更改该值，可以将[connectionpolicy.maxconnectionlimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx)设置为更高的值。

**优化分区集合的并行查询**

SQL .NET SDK 1.9.0 和更高版本支持并行查询，这使您能够并行查询分区集合。 有关详细信息，请参阅与使用这些 SDK 相关的[代码示例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)。 并行查询旨在提供比串行对应项更好的查询延迟和吞吐量。 并行查询提供两个参数，你可以根据自己的需求进行调整： 
- `MaxDegreeOfParallelism`控制可并行查询的最大分区数。 
- `MaxBufferedItemCount`控制预提取结果的数量。

***优化并行度***

并行查询的工作方式是并行查询多个分区。 但来自单个分区的数据将在查询中按顺序提取。 `MaxDegreeOfParallelism` [Sdk V2](sql-api-sdk-dotnet.md)或`MaxConcurrency` [sdk V3](sql-api-sdk-dotnet-standard.md)到分区数量的设置具有实现最高性能的查询的最佳机会，前提是所有其他系统条件保持不变。 如果不知道分区数，可以将并行度设置为较高的值。 系统会选择最小值（分区数、用户提供的输入）作为并行度。

请注意，如果数据在与查询相关的所有分区之间均匀分布，则并行查询会产生最大的好处。 如果对分区集合进行分区，以便查询返回的所有或大部分数据集中在几个分区中（一个分区是最坏的情况），则这些分区将成为查询性能的瓶颈。

***优化 MaxBufferedItemCount***
    
并行查询专用于在客户端处理当前结果批时预提取结果。 这种预提取有助于提高查询的整体延迟。 `MaxBufferedItemCount`参数限制预提取结果的数量。 设置`MaxBufferedItemCount`为预期返回的结果数（或较高的数值），以允许查询从预提取获得最大收益。

不管并行度如何，预提取的工作方式都是相同的，并且对于所有分区中的数据，都有一个缓冲区。  

**按 RetryAfter 间隔实现退让**

在性能测试期间，应该增加负载，直到限制少量请求。 如果请求受到限制，客户端应用程序应在服务器指定的重试间隔内针对中止值停止。 遵从回退，可确保在重试之间等待的时间最短。 

重试策略支持包含在以下 Sdk 中：
- [.NET sdk FOR sql](sql-api-sdk-dotnet.md)和[sql Java sdk](sql-api-sdk-java.md)版本1.8.0 和更高版本
- [适用于 sql 的 NODE.JS sdk](sql-api-sdk-node.md)和[适用于 SQL 的 Python sdk](sql-api-sdk-python.md)的版本1.9.0 和更高版本
- 所有受支持的[.Net Core](sql-api-sdk-dotnet-core.md) sdk 版本 

有关详细信息，请参阅[RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)。
    
在 .NET SDK 版本1.19 及更高版本中，有一种机制可用于记录其他诊断信息和排查延迟问题，如以下示例中所示。 可以记录具有较高读取延迟的请求的诊断字符串。 捕获的诊断字符串将帮助你了解给定请求收到429个错误的次数。

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**缓存较低读取延迟的文档 URI**

尽可能缓存文档 URI 以获得最佳读取性能。 创建资源时，需要定义逻辑来缓存资源 ID。 基于资源 Id 的查找速度比基于名称的查找更快，因此缓存这些值可提高性能。

   <a id="tune-page-size"></a>
**调整查询/读取源的页面大小以获得更好的性能**

使用读取源功能（例如`ReadDocumentFeedAsync`）执行批量文档读取时，或发出 SQL 查询时，如果结果集太大，则以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

若要减少检索所有适用的结果所需的网络往返次数，可以通过使用[最大值为最](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers)多1000标头来增加页面大小。 当你只需要显示几个结果时（例如，如果你的用户界面或应用程序 API 一次只返回10个结果），也可以将页面大小缩小为10，以降低读取和查询使用的吞吐量。

> [!NOTE] 
> 此`maxItemCount`属性不应仅用于分页。 其主要用途是通过减少单个页面中返回的最大项数来提高查询性能。  

你还可以使用可用的 Azure Cosmos DB Sdk 来设置页面大小。 中[MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) `FeedOptions`的 MaxItemCount 属性允许您设置要在枚举操作中返回的最大项数。 当`maxItemCount`设置为-1 时，SDK 会自动查找最佳值，具体取决于文档大小。 例如：
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
执行查询时，结果数据在 TCP 数据包中发送。 如果为`maxItemCount`指定的值过低，则在 TCP 数据包中发送数据所需的行程数很高，这会影响性能。 因此，如果您不确定为`maxItemCount`属性设置的值，最好将其设置为-1，并让 SDK 选择默认值。

**增加线程/任务的数目**

请参阅本文的 "网络" 部分中的[增加线程/任务的数量](#increase-threads)。

## <a name="indexing-policy"></a>索引编制策略
 
**从索引中排除未使用的路径以加快写入速度**

Azure Cosmos DB 索引策略还允许你使用索引路径（IncludedPaths 和 IndexingPolicy）指定要包括在索引中或从索引中排除的文档路径。 对于事先知道查询模式的方案，索引路径可以提高写入性能并减少索引存储。 这是因为，索引成本与索引的唯一路径数目直接相关。 例如，下面的代码演示如何使用 "*" 通配符从索引中排除文档的整个部分（子树）：

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

有关详细信息，请参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="throughput"></a>吞吐量
<a id="measure-rus"></a>

**测量和优化较低的请求单位/秒使用情况**

Azure Cosmos DB 提供了一组丰富的数据库操作。 这些操作包括使用 Udf、存储过程和触发器的关系和层次查询，所有操作都是对数据库集合中的文档进行操作。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 可以将请求单位（RU）视为执行各种数据库操作和服务应用程序请求所需的资源的单个度量，而不是考虑和管理硬件资源。

根据为每个容器设置的[请求单位](request-units.md)数来预配吞吐量。 请求单位消耗以每秒速率进行评估。 超过为容器预配的请求单位速率的应用程序将受到限制，直到速率低于容器的预配级别。 如果你的应用程序需要更高的吞吐量，则可以通过预配附加请求单位来增加吞吐量。

查询的复杂性会影响操作使用的请求单位数。 谓词数、谓词性质、Udf 数目和源数据集的大小都会影响查询操作的成本。

若要测量任何操作（创建、更新或删除）的开销，请检查[x 毫秒请求费用](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)标头（或 .net SDK 中`RequestCharge` `ResourceResponse\<T>`或`FeedResponse\<T>`的等效属性），以衡量操作所使用的请求单位数：

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

此标头中返回的请求费用是预配吞吐量的一小部分（即，2000 ru/秒）。 例如，如果前面的查询返回 1000 1 KB 的文档，则操作成本是1000。 因此，在一秒内，服务器在速率限制后期请求之前只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**处理速率限制/请求速率太大**

当客户端尝试超过帐户保留的吞吐量时，服务器的性能不会下降，并且不会使用超出保留级别的吞吐量容量。 服务器将提前通过 RequestRateTooLarge 结束请求（HTTP 状态代码429）。 它将返回一个[x 毫秒后重试](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)的标头，该标头指示用户在再次尝试请求之前必须等待的时间（以毫秒为单位）。

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

如果有多个客户端的累积操作持续超过请求速率，则当前在客户端内部设置为9的默认重试计数可能无法满足要求。 在这种情况下，客户端将向应用程序引发状态代码为429的 DocumentClientException。 

通过在`RetryOptions` `ConnectionPolicy`实例上设置，可以更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则在 30 秒的累积等待时间后将返回 DocumentClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（当前值是默认值9或用户定义的值），也会返回此错误。

自动重试行为有助于改善大多数应用程序的复原能力和可用性。 但当您执行性能基准测试时，尤其是在测量延迟时，这可能不是最佳行为。 如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。

**对于更高的吞吐量，请针对小型文档进行设计**

给定操作的请求费用（即请求处理成本）与文档大小直接相关。 大型文档的操作成本高于对小型文档进行的操作。

## <a name="next-steps"></a>后续步骤
有关用于评估多个客户端计算机上的高性能方案 Azure Cosmos DB 的示例应用程序，请参阅[使用 Azure Cosmos DB 进行性能和规模测试](performance-testing.md)。

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
