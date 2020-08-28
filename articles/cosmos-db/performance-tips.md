---
title: 适用于 .NET SDK v2 的 Azure Cosmos DB 性能提示
description: 了解用于提高 Azure Cosmos DB .NET v2 SDK 性能的客户端配置选项。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/26/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: bdf512c66958338992c5959f8e00b4589850ff33
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008363"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>适用于 Azure Cosmos DB 和 .NET SDK v2 的性能提示

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供延迟与吞吐量保证的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用一样简单。 若要了解详细信息，请参阅[如何预配容器吞吐量](how-to-provision-container-throughput.md)或[如何预配数据库吞吐量](how-to-provision-database-throughput.md)。 但是，由于 Azure Cosmos DB 是通过网络调用访问的，因此，使用 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md) 时可以通过进行客户端优化来获得最高性能。

因此，如果你尝试改善数据库性能，请考虑以下选项：

## <a name="upgrade-to-the-net-v3-sdk"></a>升级到 .NET V3 SDK

[.NET v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) 已发布。 如果使用 .NET v3 SDK，请参阅 [.NET v3 性能指南](performance-tips-dotnet-sdk-v3-sql.md)了解以下信息：

- 默认为“直接 TCP”模式
- 流 API 支持
- 支持自定义序列化程序以允许使用 System.Text.JSON
- 集成的批处理和批量操作支持

## <a name="hosting-recommendations"></a>托管方面的建议

对于查询密集型工作负载，请使用 Windows 64 位主机处理，而不要使用 Linux 或 Windows 32 位主机处理

我们建议使用 Windows 64 位主机处理来改善性能。 SQL SDK 包含一个本机 ServiceInterop.dll，用于在本地分析和优化查询。 ServiceInterop.dll 仅在 Windows x64 平台上受支持。 对于不支持 ServiceInterop.dll 的 Linux 和其他平台，将对网关发出附加的网络调用以获取优化的查询。 以下类型的应用程序默认使用 32 位主机处理。 若要将主机处理更改为 64 位处理，请根据应用程序的类型执行以下步骤：

- 对于可执行应用程序，可以通过在 "**生成**" 选项卡上的 "**项目属性**" 窗口中将 "[平台目标](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019)" 设置为 " **x64** " 来更改主机处理。

- 对于基于 VSTest 的测试项目，可以通过在 Visual Studio“测试”菜单中选择“测试” > “测试设置” > “默认处理器体系结构为 X64”，来更改主机处理。   

- 对于本地部署的 ASP.NET Web 应用程序，可以通过在“工具” > “选项” > “项目和解决方案” > “Web 项目”下选择“对网站和项目使用 IIS Express 的 64 位版”，来更改主机处理。    

- 对于部署在 Azure 上的 ASP.NET Web 应用程序，可以通过在 Azure 门户上的“应用程序设置”中选择“64 位”平台，来更改主机处理。 

> [!NOTE] 
> 新的 Visual Studio 项目默认设置为“任何 CPU”。 我们建议将项目设置为“x64”，使其不会切换到“x86”。  如果添加了仅限 x86 的依赖项，则设置为“任何 CPU”的项目可以轻松切换到“x86”。 <br/>
> ServiceInterop.dll 所处的文件夹必须是在其中执行 SDK DLL 的文件夹。 仅当你手动复制 DLL 或使用自定义的生成/部署系统时，此位置才是一个考虑因素。
    
**启用服务器端垃圾回收 (GC)**

在某些情况下，降低垃圾回收的频率可能会有帮助。 在 .NET 中，将 [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) 设置为 `true`。

**横向扩展客户端工作负载**

如果以高吞吐量级别（大于 50,000 RU/秒）进行测试，客户端应用程序可能成为瓶颈，因为计算机的 CPU 或网络利用率将达到上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

> [!NOTE] 
> CPU 使用率高可能会导致延迟增大和请求超时异常。

## <a name="networking"></a><a id="networking"></a> 网络

**连接策略：使用直接连接模式**

客户端连接到 Azure Cosmos DB 的方式对性能有重大影响，尤其是在观察到的客户端延迟方面。 可以使用这两个重要配置设置来配置客户端连接策略：连接模式和连接协议。  两种可用模式：

  * 网关模式（默认）
      
    网关模式受所有 SDK 平台支持并已配置为 [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md) 的默认设置。 如果你的应用程序在具有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口和单个 DNS 终结点。 但是，对于性能的影响是：每次在 Azure Cosmos DB 中读取或写入数据时，网关模式都涉及到额外的网络跃点。 因此，直接模式因为网络跃点较少，可以提供更好的性能。 在套接字连接数量有限的环境中运行应用程序时，我们也建议使用网关连接模式。

    在 Azure Functions 中使用 SDK 时，尤其是在[消耗计划](../azure-functions/functions-scale.md#consumption-plan)中使用时，请注意当前的[连接限制](../azure-functions/manage-connections.md)。 这种情况下，如果还在 Azure Functions 应用程序中使用其他基于 HTTP 的客户端，则使用网关模式可能更好。

  * 直接模式

    直接模式支持通过 TCP 协议的连接。
     
在直接模式下使用 TCP 时，除了网关端口外，还需确保端口范围为10000到20000，因为 Azure Cosmos DB 使用动态 TCP 端口。 当对 [专用终结点](./how-to-configure-private-endpoints.md)使用直接模式时，TCP 端口的完整范围应为0到65535。 如果这些端口未打开，而你尝试使用 TCP 协议，则会收到503服务不可用错误。 下表显示了可用于各种 Api 的连接模式以及用于每个 API 的服务端口：

|连接模式  |支持的协议  |支持的 SDK  |API/服务端口  |
|---------|---------|---------|---------|
|网关  |   HTTPS    |  所有 SDK    |   SQL (443)、MongoDB（10250、10255、10256）、表 (443)、Cassandra (10350)、Graph (443) <br> 端口10250映射到 MongoDB 实例的默认 Azure Cosmos DB API，无地域复制。 而端口10255和10256映射到具有异地复制的实例。   |
|直接    |     TCP    |  .NET SDK    | 使用公共/服务终结点时：端口介于 10000 到 20000 之间<br>使用专用终结点时：端口介于 0 到 65535 之间 |

Azure Cosmos DB 提供基于 HTTPS 的简单开放 RESTful 编程模型。 此外，它提供高效的 TCP 协议，该协议在其通信模型中也是 RESTful，可通过 .NET 客户端 SDK 获得。 TCP 协议使用 TLS 来进行初始身份验证和加密通信。 为了获得最佳性能，请尽可能使用 TCP 协议。

对于 Microsoft.Azure.DocumentDB SDK，可以在构造 `DocumentClient` 实例期间使用 `ConnectionPolicy` 参数配置连接模式。 如果使用直接模式，则也可以使用 `ConnectionPolicy` 参数设置 `Protocol`。

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

由于仅在直接模式下才支持 TCP，因此如果使用网关模式，则 HTTPS 协议始终用来与网关通信，并忽略 `ConnectionPolicy` 中的 `Protocol` 值。

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB 连接策略" border="false":::

**临时端口耗尽**

如果实例上的连接量较高或端口使用率较高，请先确认客户端实例是否为单一实例。 换句话说，客户端实例在应用程序生存期内应是唯一的。

当在 TCP 协议上运行时，客户端使用长生存期连接（而非 HTTPS 协议）来优化延迟，后者在处于非活动状态 2 分钟后即终止连接。

在具有稀疏访问且与网关模式访问相比连接计数更高的情况下，可以：

* 将 [ConnectionPolicy.PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) 属性配置为 `PrivatePortPool`（Framework 版本 >= 4.6.1 且 .NET Core 版本 >= 2.0 时有效）：此属性使 SDK 可以针对不同 Azure Cosmos DB 目标终结点使用一小部分临时端口。
* 配置 [ConnectionPolicy.IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) 属性必须大于或等于 10 分钟。 建议值介于 20 分钟到 24 小时之间。

**调用 OpenAsync 以避免首次请求时启动延迟**

默认情况下，第一个请求因为需要提取地址路由表而有较高的延迟。 使用 [SDK V2](sql-api-sdk-dotnet.md) 时，请在初始化期间调用 `OpenAsync()` 一次，以避免在首次请求时出现这种启动延迟。 调用如下所示：`await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` 会生成多个请求，这些请求用于获取帐户中所有容器的地址路由表。 如果帐户有多个容器，但其应用程序访问的是其中的一部分，则 `OpenAsync` 会生成不必要数量的流量，导致初始化速度缓慢。 因此，在这种情况下使用 `OpenAsync` 可能不起作用，因为它会降低应用程序启动速度。

**出于性能考虑，请将客户端并置在同一 Azure 区域中**

如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在 Azure Cosmos DB 数据库所在的区域。 通过大致的比较发现：在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 确保调用应用程序位于预配的 Azure Cosmos DB 终结点所在的 Azure 区域即可尽可能降低延迟。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/regions/#services)。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 连接策略" border="false":::

**增加线程/任务数目**
<a id="increase-threads"></a>

由于对 Azure Cosmos DB 的调用是通过网络执行的，因此可能需要改变请求的并行度，以便最大程度地减少客户端应用程序等待请求的时间。 例如，如果使用 .NET [任务并行库](https://msdn.microsoft.com//library/dd460717.aspx)，请创建大约数百个在 Azure Cosmos DB 中进行读取或写入操作的任务。

**启用加速网络**
 
为了降低延迟和 CPU 抖动情况，我们建议在客户端虚拟机上启用加速网络。 请参阅[创建具有加速网络的 Windows 虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)或[创建具有加速网络的 Linux 虚拟机](../virtual-network/create-vm-accelerated-networking-cli.md)。

## <a name="sdk-usage"></a>SDK 用法

**安装最新的 SDK**

Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 请参阅 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 页以了解最新的 SDK 并查看改进内容。

**在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

每个 `DocumentClient` 实例都是线程安全的，在直接模式下运行时可执行高效的连接管理和地址缓存。 若要实现有效的连接管理和提高 SDK 客户端性能，建议在应用程序的生存期内对每个 `AppDomain` 使用单个实例。

**在使用网关模式时增加每台主机的 System.Net MaxConnections**

使用网关模式时，Azure Cosmos DB 请求是通过 HTTPS/REST 发出的。 这些请求受制于每个主机名或 IP 地址的默认连接限制。 可能需要将 `MaxConnections` 设置为较大的值（100 到 1,000），以便客户端库能够同时使用多个连接来访问 Azure Cosmos DB。 在 .NET SDK 1.8.0 及更高版本中，[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 的默认值为 50。 若要更改此值，可将 [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 设置为更大的值。

**优化已分区集合的并行查询**

SQL .NET SDK 1.9.0 及更高版本支持并行查询，使你能够并行查询分区的集合。 有关详细信息，请参阅与使用这些 SDK 相关的[代码示例](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)。 并行查询旨在提供更低的查询延迟，以及优于其对应的串行查询的吞吐量。 并行查询提供两个参数，你可以根据要求优化这些参数： 
- `MaxDegreeOfParallelism` 控制可以并行查询的最大分区数。 
- `MaxBufferedItemCount` 控制预提取的结果数。

优化并行度

并行查询的工作原理是并行查询多个分区。 但就查询本身而言，会按顺序提取单个分区中的数据。 将 [SDK V2](sql-api-sdk-dotnet.md) 中的 `MaxDegreeOfParallelism` 设置为分区数最有可能实现最高性能的查询，前提是所有其他的系统条件保持不变。 如果不知道分区数，可将并行度设置为较大的数字。 系统会选择最小值（分区数、用户提供的输入）作为并行度。

如果查询时数据均衡分布在所有分区之间，则并行查询的优势最大。 如果对已分区的集合进行分区，使查询返回的全部或大部分数据集中于几个分区（最坏的情况为一个分区），则这些分区会使查询性能出现瓶颈。

优化 MaxBufferedItemCount
    
并行查询设计为当客户端正在处理当前结果批时预提取结果。 这种预提取可帮助改善查询的总体延迟。 `MaxBufferedItemCount` 参数限制预提取的结果数。 将 `MaxBufferedItemCount` 设置为预期返回的结果数（或更大的数字）可让查询通过预提取获得最大优势。

预提取的工作方式与并行度无关，使用一个单独的缓冲区来存储所有分区的数据。  

**按 RetryAfter 间隔实现退让**

在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果请求受到限制，客户端应用程序应按照服务器指定的重试间隔在限制时退让。 允许退让可确保最大程度地减少等待重试的时间。 

以下 SDK 提供重试策略支持：
- [.NET SDK for SQL](sql-api-sdk-dotnet.md) 和 [Java SDK for SQL](sql-api-sdk-java.md) 的 1.8.0 版本及更高版本
- [Node.js SDK for SQL](sql-api-sdk-node.md) 和 [Python SDK for SQL](sql-api-sdk-python.md) 的 1.9.0 版本及更高版本
- [.NET Core](sql-api-sdk-dotnet-core.md) SDK 的所有受支持版本 

有关详细信息，请参阅 [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)。
    
在 .NET SDK 1.19 及更高版本中，有一个机制可以记录附加诊断信息和排查延迟问题，如以下示例中所示。 可以记录具有较高读取延迟的请求的诊断字符串。 捕获的诊断字符串可帮助你了解收到给定请求的 429 错误的次数。

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**缓存较低读取延迟的文档 URI**

尽可能缓存文档 URI 以获得最佳读取性能。 创建资源时，需要定义用于缓存资源 ID 的逻辑。 基于资源 ID 的查找比基于名称的查找更快，因此缓存这些值可提高性能。

**调整查询/读取源的页面大小以获得更好的性能**

使用读取源功能（例如 `ReadDocumentFeedAsync`）对文档进行批量读取时，或发出 SQL 查询时，如果结果集太大，则会以分段方式返回结果。 默认情况下，以包括 100 个项的块或 1 MB 大小的块返回结果（以先达到的限制为准）。

若要减少检索所有适用结果所需的网络往返次数，可以使用 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 请求最多 1,000 个标头，以增加页面大小。 如果只需要显示几个结果（例如，用户界面或应用程序 API 一次只返回 10 个结果），也可以将页面大小减小到 10，以降低读取和查询所耗用的吞吐量。

> [!NOTE] 
> `maxItemCount` 属性不应仅用于分页目的。 它的主要用途是通过减少单个页面中返回的最大项数来提高查询性能。  

也可以使用提供的 Azure Cosmos DB SDK 设置页面大小。 `FeedOptions` 中的 [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) 属性允许你设置要在枚举操作中返回的最大项数。 当 `maxItemCount` 设置为 -1 时，SDK 会根据文档大小自动查找最佳值。 例如：
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
执行查询时，结果数据在 TCP 数据包中发送。 如果为 `maxItemCount` 指定的值太低，则在 TCP 数据包中发送数据所需的往返次数很高，这会影响性能。 因此，如果你不确定要为 `maxItemCount` 属性设置什么值，最好将其设置为 -1，让 SDK 选择默认值。

**增加线程/任务数目**

请参阅本文“网络”部分中的[增加线程/任务数目](#increase-threads)。

## <a name="indexing-policy"></a>索引编制策略
 
**从索引中排除未使用的路径以加快写入速度**

Azure Cosmos DB 的索引策略还允许使用索引路径（IndexingPolicy.IncludedPaths 和 IndexingPolicy.ExcludedPaths）指定要在索引中包括或排除的文档路径。 在事先已知查询模式的情况下，索引路径可以提高写入性能并减少索引存储。 这是因为，索引成本与已编制索引的唯一路径数目直接相关。 例如，以下代码演示如何使用“*”通配符从索引中排除文档的一整个部分（子树）：

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

有关索引的详细信息，请参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="throughput"></a><a id="measure-rus"></a> 量

**度量并优化较低的每秒请求单位使用量**

Azure Cosmos DB 提供一组丰富的数据库操作。 这些操作包括 UDF 的关系和分层查询、存储过程和触发器，全都在数据库集合中的文档上进行。 与其中每个操作关联的成本取决于完成该操作所需的 CPU、IO 和内存。 可以将请求单位 (RU) 视为执行各种数据库操作和处理应用程序请求所需的资源的单一度量，无需考虑和管理硬件资源。

吞吐量的预配取决于为每个容器设置的[请求单位](request-units.md)数。 请求单位消耗以每秒速率进行评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。

查询的复杂性会影响操作消耗的请求单位数量。 谓词数、谓词性质、UDF 数目和源数据集的大小都会影响查询操作的成本。

若要度量任一操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头（或者 .NET SDK 的 `ResourceResponse\<T>` 或 `FeedResponse\<T>` 中的等效 `RequestCharge` 属性），以度量这些操作消耗的请求单位数：

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

在此标头中返回的请求费用是已预配吞吐量（即 2,000 RU/秒）的一小部分。 例如，如果上述查询返回 1,000 个 1-KB 的文档，则操作成本是 1,000。 因此，在一秒内，服务器在对后续请求进行速率限制之前只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**处理速率限制/请求速率太大**

客户端尝试超过为帐户保留的吞吐量时，服务器的性能不会降低，并且不会使用超过保留级别的吞吐量容量。 服务器会提前结束请求并返回 RequestRateTooLarge（HTTP 状态代码 429）错误。 它会返回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头，指示重试该请求之前用户必须等待的时间（以毫秒为单位）。

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

如果累计有多个客户端持续在超过请求速率的情况下运行，则当前由客户端在内部设置为 9 的默认重试计数可能并不足够。 在此情况下，客户端会向应用程序引发 DocumentClientException，其状态代码为 429。 

可以通过在 `ConnectionPolicy` 实例上设置 `RetryOptions` 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则在 30 秒的累积等待时间后返回 DocumentClientException 和状态代码 429。 即使当前的重试计数小于最大重试计数（无论当前值是默认值 9 还是用户定义的值），也会返回此错误。

自动重试行为有助于改善大多数应用程序的复原能力和可用性。 但是，在执行性能基准测试时（尤其是在度量延迟时），自动重试可能不是最佳行为。 如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，并确保请求以低于保留请求速率的方式运行。 有关详细信息，请参阅[请求单位](request-units.md)。

**针对较小文档进行设计以提高吞吐量**

给定操作的请求费用（即请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。

## <a name="next-steps"></a>后续步骤

如果需要使用一个示例应用程序来评估 Azure Cosmos DB，以便在数个客户端计算机上实现高性能方案，请参阅[使用 Azure Cosmos DB 进行性能和缩放测试](performance-testing.md)。

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
