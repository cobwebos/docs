---
title: 适用于 .NET SDK v3 的 Azure Cosmos DB 性能提示
description: 了解有助于提高 Azure Cosmos DB .NET v3 SDK 性能的客户端配置选项。
author: j82w
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: jawilley
ms.custom: devx-track-dotnet
ms.openlocfilehash: f8e610531eaf3e7e5dbee9c40c88683a05029303
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802984"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>适用于 Azure Cosmos DB 和 .NET 的性能提示

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB 是一个快速、弹性的分布式数据库，可以在提供有保证的延迟与吞吐量级别的情况下无缝缩放。 凭借 Azure Cosmos DB，无需对体系结构进行重大更改或编写复杂的代码即可缩放数据库。 扩展和缩减操作就像执行单个 API 调用一样简单。 若要了解详细信息，请参阅[预配容器吞吐量](how-to-provision-container-throughput.md)或[预配数据库吞吐量](how-to-provision-database-throughput.md)。 

因为 Azure Cosmos DB 是通过网络调用访问的，因此，使用 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md) 时可以通过进行客户端优化来获得最高性能。

如果你尝试改善数据库性能，请考虑以下部分中提供的选项。

## <a name="hosting-recommendations"></a>托管方面的建议

对于查询密集型工作负载，请使用 Windows 64 位主机处理，而不要使用 Linux 或 Windows 32 位主机处理

我们建议使用 Windows 64 位主机处理来改善性能。 SQL SDK 包含一个本机 ServiceInterop.dll，用于在本地分析和优化查询。 ServiceInterop.dll 仅在 Windows x64 平台上受支持。 

对于不支持 ServiceInterop.dll 的 Linux 和其他平台，将对网关发出附加的网络调用以获取优化的查询。 

此处列出的四个应用程序类型默认使用 32 位主机处理。 若要将你的应用程序类型的主机处理更改为 64 位处理，请执行以下步骤：

- **对于可执行应用程序**：在“项目属性”窗口的“生成”窗格上，将[平台目标](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019&preserve-view=true)设置为“x64”。

- **对于基于 VSTest 的测试项目**：在 Visual Studio“测试”菜单上，选择“测试” > “测试设置”，然后将“默认处理器体系结构”设置为“X64”。  

- **对于本地部署的 ASP.NET Web 应用程序**：选择“工具” > “选项” > “项目和解决方案” > “Web 项目”，然后选择“为网站和项目使用 64 位版本的 IIS Express”。    

- **对于在 Azure 上部署的 ASP.NET Web 应用程序**：在 Azure 门户的“应用程序设置”中，选择“64 位”平台。

> [!NOTE] 
> 新的 Visual Studio 项目默认设置为“任何 CPU”。 我们建议将项目设置为“x64”，使其不会切换到“x86”。  如果添加了仅限 x86 的依赖项，则设置为“任何 CPU”的项目可以轻松切换到“x86”。 <br/>
> ServiceInterop.dll 文件所处的文件夹必须是在其中执行 SDK DLL 的文件夹。 仅当你手动复制 DLL 或使用自定义的生成或部署系统时，这才是一个考虑因素。
    
**启用服务器端垃圾回收**

在某些情况下，降低垃圾回收的频率可能会有帮助。 在 .NET 中，将 [gcServer](https://docs.microsoft.com/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) 设置为 `true`。

**横向扩展客户端工作负载**

如果你在高吞吐量级别或速率大于 50,000 请求单位/秒（RU/秒）的情况下进行测试，则客户端应用程序可能会成为工作负荷瓶颈。 这是因为计算机可能会达到 CPU 或网络利用率的上限。 如果达到此上限，可以跨多个服务器横向扩展客户端应用程序以继续进一步推送 Azure Cosmos DB 帐户。

> [!NOTE] 
> CPU 使用率高可能会导致延迟增大和请求超时异常。

## <a name="networking"></a>网络
<a id="direct-connection"></a>

**连接策略：使用直接连接模式**

客户端连接到 Azure Cosmos DB 的方式对性能有重大影响，尤其是在观察到的客户端延迟方面。 有两个密钥配置设置可用于配置客户端连接策略：连接模式和连接协议。  两种可用的连接模式为：

   * 直接模式（默认）

     直接模式支持通过 TCP 协议进行连接，在使用 [Microsoft.Azure.Cosmos/.NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) 的情况下是默认的连接模式。 与网关模式相比，直接模式提供的性能更佳且需要的网络跃点数更少。

   * 网关模式
      
     如果应用程序在有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口与单个终结点。 
     
     但是，对于性能的影响是：每次在 Azure Cosmos DB 中读取或写入数据时，网关模式都涉及到额外的网络跃点。 因此，直接模式因为网络跃点较少，可以提供更好的性能。 在套接字连接数量有限的环境中运行应用程序时，我们也建议使用网关连接模式。

     在 Azure Functions 中使用 SDK 时，尤其是在[消耗计划](../azure-functions/functions-scale.md#consumption-plan)中使用时，请注意当前的[连接限制](../azure-functions/manage-connections.md)。 这种情况下，如果还在 Azure Functions 应用程序中使用其他基于 HTTP 的客户端，则使用网关模式可能更好。
     
在直接模式下使用 TCP 协议时，除了网关端口外，还需确保从 10000 到 20000 这个范围内的端口处于打开状态，因为 Azure Cosmos DB 使用动态 TCP 端口。 在[专用终结点](./how-to-configure-private-endpoints.md)上使用直接模式时，应打开从 0 到 65535 的整个 TCP 端口范围。 默认情况下，标准 Azure VM 配置的端口是打开的。 如果这些端口未处于打开状态，你在尝试使用 TCP 时将收到“503 服务不可用”错误。 

下表显示了可用于各种 API 的连接模式，以及用于每个 API 的服务端口：

|连接模式  |支持的协议  |支持的 SDK  |API/服务端口  |
|---------|---------|---------|---------|
|网关  |   HTTPS    |  所有 SDK    |   SQL (443)、MongoDB（10250、10255、10256）、表 (443)、Cassandra (10350)、Graph (443) <br><br> 端口 10250 映射到没有异地复制功能的默认 Azure Cosmos DB API for MongoDB 实例，端口 10255 和 10256 映射到具有异地复制功能的实例。   |
|直接    |     TCP    |  .NET SDK    | 使用公共/服务终结点时：端口介于 10000 到 20000 之间<br><br>使用专用终结点时：端口介于 0 到 65535 之间 |

Azure Cosmos DB 提供基于 HTTPS 的简单开放 RESTful 编程模型。 此外，它提供高效的 TCP 协议，该协议在其通信模型中也是 RESTful，可通过 .NET 客户端 SDK 获得。 TCP 协议使用传输层安全性 (TLS) 来进行初始身份验证和加密通信。 为了获得最佳性能，请尽可能使用 TCP 协议。

对于 SDK V3，可以在 `CosmosClientOptions` 中创建 `CosmosClient` 实例时配置连接模式。 请记住，直接模式是默认设置。

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

由于仅在直接模式下才支持 TCP，如果使用网关模式，则 HTTPS 协议始终用来与网关通信。

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="使用不同的连接模式和协议建立到 Azure Cosmos DB 的连接。" border="false":::

**临时端口耗尽**

如果实例上的连接量较高或端口使用率较高，请先确认客户端实例是否为单一实例。 换句话说，客户端实例在应用程序生存期内应是唯一的。

在 TCP 协议上运行时，客户端使用长生存期连接来优化延迟。 这与 HTTPS 协议相反，后者在处于非活动状态两分钟后终止连接。

在具有稀疏访问且与网关模式访问相比连接计数更高的情况下，你可以：

* 将 [CosmosClientOptions.PortReuseMode](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) 属性配置为 `PrivatePortPool`（Framework 版本 >= 4.6.1 且 .NET Core 版本 >= 2.0 时有效）。 此属性使 SDK 可以针对各种 Azure Cosmos DB 目标终结点使用一小部分临时端口。
* 将 [CosmosClientOptions.IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) 属性配置为大于或等于 10 分钟。 建议值为 20 分钟到 24 小时。

<a id="same-region"></a>

**出于性能考虑，请将客户端并置在同一 Azure 区域中**

如果可能，请将任何调用 Azure Cosmos DB 的应用程序放在 Azure Cosmos DB 数据库所在的区域。 通过大致的比较发现：在同一区域中对 Azure Cosmos DB 的调用可在 1-2 毫秒内完成，而美国西海岸和美国东海岸之间的延迟则大于 50 毫秒。 根据请求采用的路由，各项请求从客户端传递到 Azure 数据中心边界时的此类延迟可能有所不同。 

确保调用应用程序位于预配的 Azure Cosmos DB 终结点所在的 Azure 区域即可尽可能降低延迟。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/regions/#services)。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="使用不同的连接模式和协议建立到 Azure Cosmos DB 的连接。" border="false":::

   <a id="increase-threads"></a>

**增加线程/任务数目**

由于对 Azure Cosmos DB 的调用是通过网络执行的，可能需要改变请求的并发度，以便最大程度地减少客户端应用程序等待请求的时间。 例如，如果使用 .NET [任务并行库](https://msdn.microsoft.com//library/dd460717.aspx)，请创建大约数百个在 Azure Cosmos DB 中进行读取或写入操作的任务。

**启用加速网络**
 
为了降低延迟和 CPU 抖动情况，建议在客户端虚拟机上启用加速网络。 有关详细信息，请参阅[创建具有加速网络的 Windows 虚拟机](../virtual-network/create-vm-accelerated-networking-powershell.md)或[创建具有加速网络的 Linux 虚拟机](../virtual-network/create-vm-accelerated-networking-cli.md)。

## <a name="sdk-usage"></a>SDK 用法

**安装最新的 SDK**

Azure Cosmos DB SDK 正在不断改进以提供最佳性能。 若要确定最新的 SDK 并查看改进内容，请参阅 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md)。

**使用流 API**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 包含的流 API 可以在不序列化的情况下接收和返回数据。 

如果中间层应用程序不直接使用 SDK 的响应，而是将其中继到其他应用程序层，则此类应用程序可以受益于流 API。 有关流处理的示例，请参阅[项管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement)示例。

**在应用程序生存期内使用单一实例 Azure Cosmos DB 客户端**

每个 `CosmosClient` 实例都是线程安全的，在直接模式下运行时可以进行高效的连接管理和地址缓存。 若要实现有效的连接管理和提高 SDK 客户端性能，建议在应用程序的生存期内对每个 `AppDomain` 使用单个实例。

使用 Azure Functions 时，实例还应遵循现有[指南](../azure-functions/manage-connections.md#static-clients)并维护单个实例。

<a id="max-connection"></a>

**禁用写入操作时的内容响应**

对于具有大量创建有效负载的工作负荷，请将 `EnableContentResponseOnWrite` 请求选项设置为 `false`。 该服务将不再将创建或更新的资源返回给 SDK。 通常，因为应用程序具有正在创建的对象，因此不需要服务即可将其返回。 标头值仍可访问，例如请求费用。 禁用内容响应有助于提高性能，因为 SDK 不再需要分配内存或序列化响应正文。 此外还会降低网络带宽的使用率，从而进一步提高性能。  

```csharp
ItemRequestOption requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**启用 Bulk 以优化吞吐量，而不是优化延迟**

对于工作负荷需要大吞吐量且延迟不是那么重要的场景，请启用“Bulk”。 若要详细了解如何启用 Bulk 功能，并了解应将其用于哪些场景，请参阅 [Bulk 支持简介](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk)。

**在使用网关模式时增大每台主机的 System.Net MaxConnections**

使用网关模式时，Azure Cosmos DB 请求是通过 HTTPS/REST 发出的。 这些请求受制于每个主机名或 IP 地址的默认连接限制。 可能需要将 `MaxConnections` 设置为较大的值（从 100 到 1,000），以便客户端库能够同时使用多个连接来访问 Azure Cosmos DB。 在 .NET SDK 1.8.0 及更高版本中，[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 的默认值为 50。 若要更改该值，可以将 [`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 设置为较高的值。

**优化已分区集合的并行查询**

SQL .NET SDK 支持并行查询，使你能够并行查询分区的容器。 有关详细信息，请参阅与使用这些 SDK 相关的[代码示例](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs)。 并行查询旨在提供更低的查询延迟，以及优于其对应的串行查询的吞吐量。 

并行查询提供两个参数，你可以根据要求优化这些参数： 

- **MaxConcurrency**：控制可以并行查询的最大分区数。

   并行查询的工作原理是并行查询多个分区。 但就查询本身而言，会按顺序提取单个分区中的数据。 将 [SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 中的 `MaxConcurrency` 设置为分区数最有可能实现最高性能的查询，前提是所有其他的系统条件保持不变。 如果不知道分区数，可将并行度设置为较大的数字。 系统会选择最小值（分区数、用户提供的输入）作为并行度。

    如果查询时数据均衡分布在所有分区之间，则并行查询的优势最大。 如果对已分区的集合进行分区，使查询返回的全部或大部分数据集中于几个分区（最坏的情况为一个分区），则这些分区会使查询性能出现瓶颈。
   
- **MaxBufferedItemCount**：控制预提取的结果数。

   并行查询设计为当客户端正在处理当前结果批时预提取结果。 这种预提取可帮助改善查询的总体延迟。 `MaxBufferedItemCount` 参数限制预提取的结果数。 将 `MaxBufferedItemCount` 设置为预期返回的结果数（或更大的数字）可让查询通过预提取获得最大优势。

   预提取的工作方式与并行度无关，使用一个单独的缓冲区来存储所有分区的数据。  

**按 RetryAfter 间隔实现退让**

在性能测试期间，应该增加负载，直到系统对小部分请求进行限制为止。 如果请求受到限制，客户端应用程序应按照服务器指定的重试间隔在限制时退让。 允许退让有助于确保最大程度地减少等待重试的时间。 

有关详细信息，请参阅 [RetryAfter](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_CosmosException_RetryAfter)。
    
有一个机制可以记录附加诊断信息和排查延迟问题，如以下示例所示。 可以记录具有较高读取延迟的请求的诊断字符串。 捕获的诊断字符串可帮助你了解收到给定请求的 429 错误的次数。

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**增加线程/任务数目**

请参阅本文“网络”部分中的[增加线程/任务数目](#increase-threads)。

## <a name="indexing-policy"></a>索引策略
 
**从索引中排除未使用的路径以加快写入速度**

Azure Cosmos DB 的索引策略还允许使用索引路径（IndexingPolicy.IncludedPaths 和 IndexingPolicy.ExcludedPaths）指定要在索引中包括或排除的文档路径。 

在事先已知查询模式的情况下，仅对所需路径编制索引可以提高写入性能，减少写入操作时的 RU 费用和索引存储。 这是因为，索引成本与已编制索引的唯一路径数目直接相关。 例如，以下代码演示了如何使用“*”通配符从索引中排除整个文档部分（子树）：

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

有关索引的详细信息，请参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="throughput"></a>吞吐量
<a id="measure-rus"></a>

**度量和优化 RU/秒使用量较低的情况**

Azure Cosmos DB 提供一组丰富的数据库操作。 这些操作包括通用磁盘格式 (UDF) 文件的关系和分层查询、存储过程和触发器，全都在数据库集合中的文档上进行。 

与其中每个操作关联的成本取决于完成该操作所需的 CPU、IO 和内存。 可以考虑将请求单位作为所需资源的单个度量，以便执行各种数据库操作和服务应用程序请求，而不必考虑和管理硬件资源。

吞吐量的预配取决于为每个容器设置的[请求单位](request-units.md)数。 请求单位消耗以每秒单位数速率进行评估。 如果应用程序的速率超过了为其容器预配的请求单位速率，则会受到限制，直到该速率降到容器的预配级别以下。 如果应用程序需要较高级别的吞吐量，可以通过预配更多请求单位来增加吞吐量。

查询的复杂性会影响操作消耗的请求单位数量。 谓词数、谓词性质、UDF 文件数目和源数据集的大小都会影响查询操作的成本。

若要度量任一操作（创建、更新或删除）的开销，请检查 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头（或者 .NET SDK 的 `ResourceResponse\<T>` 或 `FeedResponse\<T>` 中的等效 `RequestCharge` 属性），以度量这些操作消耗的请求单位数：

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

在此标头中返回的请求费用是已预配吞吐量（即 2,000 RU/秒）的一小部分。 例如，如果上述查询返回 1,000 个 1-KB 的文档，则操作成本是 1,000。 因此，在一秒内，服务器在对后续请求进行速率限制之前只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)和[请求单位计算器](https://www.documentdb.com/capacityplanner)。
<a id="429"></a>

**处理速率限制/请求速率太大**

客户端尝试超过为帐户保留的吞吐量时，服务器的性能不会降低，并且不会使用超过保留级别的吞吐量容量。 服务器会提前结束请求并返回 RequestRateTooLarge（HTTP 状态代码为 429）错误。 它会返回 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 标头，指示重试该请求之前用户必须等待的时间（以毫秒为单位）。

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

SDK 全部都会隐式捕获此响应，并遵循服务器指定的 retry-after 标头，并重试请求。 除非多个客户端同时访问帐户，否则下次重试就会成功。

如果累计有多个客户端持续在超过请求速率的情况下运行，则当前由客户端在内部设置为 9 的默认重试计数可能并不够用。 在这种情况下，客户端会向应用程序引发 CosmosException，状态代码为 429。 

可以通过在 `CosmosClientOptions` 实例上设置 `RetryOptions` 来更改默认重试计数。 默认情况下，如果请求继续以高于请求速率的方式运行，则会在 30 秒的累积等待时间后返回 CosmosException，状态代码为 429。 即使当前的重试计数小于最大重试计数（无论当前值是默认值 9 还是用户定义的值），也会返回此错误。

自动重试行为有助于改善大多数应用程序的复原能力和可用性。 但是，在执行性能基准测试时（尤其是在度量延迟时），自动重试可能不是最佳行为。 如果实验达到服务器限制并导致客户端 SDK 静默重试，则客户端观测到的延迟会剧增。 若要避免性能实验期间出现延迟高峰，可以测量每个操作返回的费用，确保请求以低于保留请求速率的方式运行。 

有关详细信息，请参阅[请求单位](request-units.md)。

**针对较小文档进行设计以提高吞吐量**

指定操作的请求费用（即请求处理成本）与文档大小直接相关。 大型文档的操作成本高于小型文档的操作成本。

## <a name="next-steps"></a>后续步骤
如果需要使用一个示例应用程序来评估 Azure Cosmos DB，以便在数个客户端计算机上实现高性能方案，请参阅[使用 Azure Cosmos DB 进行性能和缩放测试](performance-testing.md)。

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
