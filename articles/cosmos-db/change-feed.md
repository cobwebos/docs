---
title: "使用 Azure Cosmos DB 中的更改源支持 | Microsoft Docs"
description: "使用 Azure Cosmos DB 的更改源支持跟踪文档中发生的更改，执行基于事件的处理（例如触发器），使缓存和分析系统保持最新状态。"
keywords: "更改源"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 160fbc98e0f3dcc7d17cbe0c7f7425811596a896
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的更改源支持
[Azure Cosmos DB](../cosmos-db/introduction.md) 是快速灵活的全球复制数据库服务，用于存储大量事务与操作数据，读取和写入时的延迟为个位数的毫秒且可预测。 它非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是跟踪对 Azure Cosmos DB 数据所做的更改、更新具体化的视图、执行实时分析、将数据存档到冷存储，以及在发生特定事件时根据这些更改触发通知。 使用 Azure Cosmos DB 中的更改源支持，可针对每种模式构建高效、可扩展的解决方案。

利用更改源支持，Azure Cosmos DB 在 Azure Cosmos DB 集合中按文档修改顺序提供排序的文档列表。 此源可用于侦听对集合中数据所做的修改，以及执行如下操作：

* 插入或修改文档时触发 API 调用
* 针对更新执行实时（流）处理
* 将数据与缓存、搜索引擎或数据仓库同步

Azure Cosmos DB 中发生的更改可以保存、以异步方式进行处理，以及分发到一个或多个使用者供并行处理。 了解更改源的 API，以及如何使用它们构建可缩放的实时应用程序。 本文介绍如何使用 Azure Cosmos DB 更改源及 DocumentDB API。 

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 目前，更改源支持仅提供给 DocumentDB API；尚不支持图形 API 和表 API。

## <a name="use-cases-and-scenarios"></a>用例和方案
使用更改源可对具有大量写入操作的大型数据集进行有效处理，这样就不需要查询整个数据集来识别发生了哪些更改。 例如，可以有效地执行以下任务：

* 使用 Azure Cosmos DB 中存储的数据更新缓存、搜索索引或数据仓库。
* 实现应用程序级别的数据分层和存档，即，将“热数据”存储在 Azure Cosmos DB 中，将“冷数据”搁置在 [Azure Blob 存储](../storage/common/storage-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 中。
* 使用 [Apache Hadoop](run-hadoop-with-hdinsight.md) 实现数据批量分析。
* 使用 Azure Cosmos DB [在 Azure 上实现 lambda 管道](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)。 Azure Cosmos DB 提供一种可缩放的数据库解决方案，该解决方案可处理引入和查询，实现 TCO 较低的 lambda 体系结构。 
* 在不造成任何停机的情况下迁移到使用不同分区方案的另一个 Azure Cosmos DB 帐户。

基于 Azure Cosmos DB 用于引入和查询的 lambda 管道：

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

可以使用 Azure Cosmos DB 接收和存储从设备、传感器、基础架构和应用程序发出的事件数据，并使用 [Azure 流分析](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md) 或 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) 实时处理这些事件。 

在[无服务器的](http://azure.com/serverless) Web 应用和移动应用中，可以跟踪各种事件（例如，对客户配置文件、首选项或位置的更改），以触发特定的操作，例如，使用 [Azure Functions](../azure-functions/functions-bindings-documentdb.md) 或[应用服务](https://azure.microsoft.com/services/app-service/)向客户的设备发送推送通知。 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。

## <a name="how-change-feed-works-in-azure-cosmos-db"></a>更改源在 Azure Cosmos DB 中的工作原理
Azure Cosmos DB 能够以增量方式读取对 Azure Cosmos DB 集合的更新。 此更改源具有以下属性：

* 更改将保存在 Azure Cosmos DB 中，并可以异步方式进行处理。
* 对集合中的文档所做的更改将立即在更改源中出现。
* 在更改源中，对文档的每个更改都将显示一次，且客户端管理其检查点逻辑。 更改源处理器库提供自动检查点和“至少一次”语义。
* 更改日志中仅包含最近对给定文档所做的更改， 而不包含中途的更改。
* 更改源按照每个分区键值中的修改顺序排序。 无法保证各分区键值中的顺序一致。
* 更改可从任意时间点同步，也就是说，发生更改的数据没有固定的保留期。
* 更改以分区键范围区块提供。 多个使用者/服务器可以使用此功能并行处理大型集合中发生的更改。
* 应用程序可针对同一集合同时请求多个更改源。

默认情况下，所有帐户中启用 Azure Cosmos DB 更改源。 可使用写入区域或任何[读取区域](distribute-data-globally.md)中的[预配吞吐量](request-units.md)从更改源中读取数据，就像在 Azure Cosmos DB 中执行其他任何操作一样。 更改源包括针对集合中的文档所做的插入和更新操作。 可以通过在文档中的删除位置设置“软删除”标志来捕获删除操作。 或者，可以通过 [TTL 功能](time-to-live.md)为文档设置有限的过期期限（例如 24 小时），并使用该属性的值捕获删除操作。 使用此解决方案时，处理更改的时间间隔必须比 TTL 过期期限要短。 更改源适用于文档集合中的每个分区键范围，因此，可以分散到一个或多个使用者供并行处理。 

![Azure Cosmos DB 更改源的分布式处理](./media/change-feed/changefeedvisual.png)

可通过几种方法在客户端代码中实现更改源。 接下来的几部分介绍如何使用 Azure Cosmos DB REST API 和 DocumentDB SDK 实现更改源。 但是，对于.NET 应用程序，建议使用新的[更改源处理器库](#change-feed-processor)来处理来自更改源的事件，因为其简化了跨分区读取更改并可实现多个线程并行工作。 

## <a id="rest-apis"></a>使用 REST API 和 DocumentDB SDK
Azure Cosmos DB 提供名为集合的弹性存储和吞吐量容器。 集合中的数据已使用[分区键](partition-data.md)进行逻辑分组，以提高可伸缩性与性能。 Azure Cosmos DB 提供各种 API 来访问这些数据，包括按 ID（读取/获取）、查询和读取源（扫描）进行查找。 可通过在 DocumentDB `ReadDocumentFeed` API 中填充两个新请求标头来获取更改源，并跨多个分区键范围并行处理更改源。

### <a name="readdocumentfeed-api"></a>ReadDocumentFeed API
让我们简单了解一下 ReadDocumentFeed 的工作原理。 Azure Cosmos DB 支持通过 `ReadDocumentFeed` API 读取集合中文档的源。 例如，以下请求返回 `serverlogs` 集合中的文档页面。 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

可以使用 `x-ms-max-item-count` 限制结果；可以通过使用前一响应中返回的 `x-ms-continuation` 标头重新提交请求来恢复读取。 在单个客户端中执行时，`ReadDocumentFeed` 以串行方式循环访问各分区的结果。 

**串行读取文档源**

还可使用某个支持的 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 检索文档源。 例如，下面的代码片段演示了如何在 .NET 中使用 [ReadDocumentFeedAsync 方法](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync?view=azure-dotnet)。

```csharp
FeedResponse<dynamic> feedResponse = null;
do
{
    feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
}
while (feedResponse.ResponseContinuation != null);
```

### <a name="distributed-execution-of-readdocumentfeed"></a>ReadDocumentFeed 的分布式执行
对于包含 TB 量级数据的集合，或者在引入大量更新的情况下，从一台客户端计算机以串行方式执行源读取可能不可行。 为了支持这些大数据方案，Azure Cosmos DB 提供了相应的 API，以透明方式在多个客户端读取者/使用者之间分布 `ReadDocumentFeed` 调用。 

**分布式读取文档源**

为了针对增量更改提供可缩放的处理，Azure Cosmos DB 根据分区键的范围提供更改源 API 的扩展模型支持。

* 执行 `ReadPartitionKeyRanges` 调用可以获取集合的分区键范围列表。 
* 对于每个分区键范围，可以执行 `ReadDocumentFeed` 来读取具有该范围内的分区键的文档。

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>检索集合的分区键范围
可以通过请求集合中的 `pkranges` 资源来检索分区键范围。 例如，以下请求检索 `serverlogs` 集合的分区键范围列表：

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

此请求返回以下响应，其中包含有关分区键范围的元数据：

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**分区键范围属性**：每个分区键范围包括下表中的元数据属性：

<table>
    <tr>
        <th>标头名称</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>分区键范围的 ID。 此 ID 在每个集合中是固定且唯一的。</p>
            <p>必须在以下调用中使用此属性才能按分区键范围读取更改。</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>分区键范围的最大分区键哈希值。 供内部使用。</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>分区键范围的最小分区键哈希值。 供内部使用。</td>
    </tr>       
</table>

可使用某个支持的 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 完成此操作。 例如：下列代码片段显示了如何使用 [ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync?view=azure-dotnet) 方法，在 .NET 中检索分区键范围。

```csharp
string pkRangesResponseContinuation = null;
List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

do
{
    FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri, 
        new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
}
while (pkRangesResponseContinuation != null);
```

Azure Cosmos DB 支持通过设置可选 `x-ms-documentdb-partitionkeyrangeid` 标头按分区键范围检索文档。 

### <a name="performing-an-incremental-readdocumentfeed"></a>执行增量 ReadDocumentFeed
ReadDocumentFeed 支持使用以下方案/任务对 Azure Cosmos DB 集合中的更改进行增量处理：

* 读取自始至终（从创建集合时开始）对文档所做的全部更改。
* 读取从当前时间开始对文档的未来更新所作的全部更改，或从用户指定的时间开始所作的任何更改。
* 通过集合的逻辑版本 (ETag) 读取对文档所做的全部更改。 可以通过增量读取源请求根据返回的 ETag 设置使用者检查点。

这些更改包括文档插入和更新。 若要捕获删除操作，必须在文档中使用“软删除”属性，或使用[内置的 TTL 属性](time-to-live.md)在更改源中发出待删除信号。

下表为 ReadDocumentFeed 操作列出了[请求](/rest/api/documentdb/common-documentdb-rest-request-headers.md)和[响应标头](/rest/api/documentdb/common-documentdb-rest-response-headers.md)。

**增量 ReadDocumentFeed 的请求标头**：

<table>
    <tr>
        <th>标头名称</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>必须设置为“Incremental feed”，否则省略</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>无标头：返回从一开始（创建集合时）所做的全部更改</p>
            <p>"*"：返回最近对集合中数据所做的全部更改</p>           
            <p>&lt;etag&gt;：如果设置为集合 ETag，则返回从该逻辑时间戳开始所做的全部更改</p>
        </td>
    </tr>
    <tr>    
        <td>If-Modified-Since</td> 
        <td>RFC 1123 时间格式；如果指定 If-None-Match，则忽略</td> 
    </tr> 
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>用于读取数据的分区键范围 ID。</td>
    </tr>
</table>

**增量 ReadDocumentFeed 的响应标头**：

<table> <tr>
        <th>标头名称</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>响应中返回的最后一个文档的逻辑序列号 (LSN)。</p>
            <p>在 If-None-Match 中重新提交此值可以恢复增量 ReadDocumentFeed。</p>
        </td>
    </tr>
</table>

以下示例请求通过逻辑版本/ ETag `28535` 和分区键范围 `16` 返回集合中发生的所有增量更改：

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

更改已按分区键范围内每个分区键值中的时间排序。 无法保证各分区键值中的顺序一致。 如果结果太多，无法在一个页面中显示，可以使用 `If-None-Match` 标头（其值等于前一响应中的 `etag`）重新提交请求来阅读下一页结果。 如果在存储过程或触发器中以事务方式插入或更新了多个文档，这些文档都会在同一个响应页面中返回。

> [!NOTE]
> 通过更改源，在存储过程或触发器中插入或更新了多个文档的情况下，页面中可能会返回比 `x-ms-max-item-count` 中指定的数目更多的项。 

使用 .NET SDK (1.17.0) 时，在 `ChangeFeedOptions` 中设置字段 `StartTime`，以在调用 `CreateDocumentChangeFeedQuery` 时直接返回自 `StartTime` 开始更改的文档。 通过使用 REST API 指定 `If-Modified-Since`，请求将返回的不是文档本身，而是继续标记或响应标头中的 `etag`。 若要返回指定时间修改的文档，必须在下一个请求中将继续标记 `etag` 与 `If-None-Match` 配合使用，以返回实际文档。 

.NET SDK 提供 [CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery?view=azure-dotnet) 和 [ChangeFeedOptions](/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet)帮助器类，以访问对集合进行的更改。 以下代码片段演示如何在单个客户端中使用.NET SDK 检索从一开始所做的全部更改。

```csharp
private async Task<Dictionary<string, string>> GetChanges(
    DocumentClient client,
    string collection,
    Dictionary<string, string> checkpoints)
{
    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

    foreach (PartitionKeyRange pkRange in partitionKeyRanges)
    {
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);

        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collection,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = 1
            });

        while (query.HasMoreResults)
        {
            FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>();

            foreach (DeviceReading changedDocument in readChangesResponse)
            {
                Console.WriteLine(changedDocument.Id);
            }

            checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
        }
    }

    return checkpoints;
}
```
以下片段演示如何使用更改源支持和上述函数，在 Azure Cosmos DB 中实时处理更改。 第一个调用返回集合中的所有文档，第二个调用仅返回自上一个检查点后创建的两个文档。

```csharp
// Returns all documents in the collection.
Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

// Returns only the two documents created above.
checkpoints = await GetChanges(client, collection, checkpoints);
```

还可以筛选更改源 - 使用客户端逻辑有选择性地处理事件即可。 例如，以下代码片段使用客户端 LINQ 专门处理设备传感器发送的温度更改事件。

```csharp
FeedResponse<DeviceReading> readChangesResponse = await query.ExecuteNextAsync<DeviceReading>;

foreach (DeviceReading changedDocument in 
    readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
{
    // trigger an action, like call an API
}
```

## <a id="change-feed-processor"></a>更改源处理器库
还可选择使用 [Azure Cosmos DB 更改源处理器库](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed)，该库有助于从跨多个使用者的更改源轻松分发事件处理。 该库非常适合在 .NET 平台上创建更改源读取器。 可通过包含在其他 Cosmos DB SDK 中的方法使用更改源处理器库简化的一些工作流包括： 

* 当数据跨多个分区存储时，从更改源拉取更新
* 将数据从一个集合移动或复制到另一个集合
* 数据和更改源的更新触发的操作的并行执行 

在 Cosmos SDK 中使用 API 为每个分区中的更改源更新提供精确的访问权限，且使用更改源处理器库可以跨分区和多个并行运行的线程简化读取更改。 更改源处理器使用租用机制自动管理跨分区的读取更改，而不是手动读取每个容器的更改、为每个分区保存一个继续标记。

库作为 NuGet 包提供：[Microsoft.Azure.Documents.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)可从作为 Github[ 示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)的源代码中获得。 

### <a name="understanding-change-feed-processor-library"></a>了解更改源处理器库 

实现更改源处理器需要四个主要组件：监视集合、租用集合、处理器主机和使用者。 

监视集合：监视集合是生成更改源的数据。 对监视集合的任何插入和更改都会反映在集合的更改源中。 

租用集合：租用集合协调处理跨多个辅助角色的更改源。 单独集合用于存储租用，一个分区一个租用。 最好将此租用集合存储在不同的帐户（写入区域更靠近更改源处理器运行位置）。 租用对象有以下属性： 
* 所有者：指定拥有租用的主机
* 继续：为特殊分区指定更改源中的位置（继续标记）
* 时间戳：租用最近更新时间；时间戳可用于检查租用是否到期 

处理器主机：每个主机根据具有活动租用的主机其他实例数目，确定要处理的分区数目。 
1.  主机启动时，将获取租用，以在所有主机中均衡工作负荷。 主机定期续订租用，使租用保持活动状态。 
2.  主机为每次读取检查其租用的最近继续标记。 为确保并发安全，主机会检查 ETag 的每次租用更新。 此外还支持其他检查点策略。  
3.  关闭后，主机会释放所有租用，但保留继续信息，以便稍后从存储检查点继续读取。 

此时，主机数量不能大于分区（租用）数量。

使用者：使用者或辅助角色是执行每个主机启动的更改源处理的线程。 每个处理器主机可以有多个使用者。 每个使用者从分配给其的分区上读取更改源，并就更改和过期的租用通知主机。

若要进一步了解更改源处理器的四个元素是如何协同工作的，请看下图中的一个示例。 监视集合存储文档，并将“city”用作分区键。 可以看到蓝色分区包含“A - E”中的“city”字段的文档。 有两个主机，每个主机有两个从四个并行分区读取的使用者。 箭头显示的是从更改源中特定位置读取的使用者。 在第一个分区中，深蓝色表示更改源上未读取的更改，而浅蓝色表示更改源上已读取的更改。 主机使用租用集合来存储“继续”值，跟踪每个使用者的当前读取位置。 

![使用 Azure Cosmos DB 更改源处理器主机](./media/change-feed/changefeedprocessornew.png)

### <a name="using-change-feed-processor-library"></a>使用更改源处理器库 
以下部分介绍如何在将更新从源集合复制到目标集合的上下文中，使用更改源处理器库。 在此处，源集合是更改源处理器中的监视集合。 

安装并添加更改源处理器 NuGet 包 

安装更改源处理器 NuGet 包之前，请先安装： 
* Microsoft.Azure.DocumentDB 1.13.1 或更高版本 
* Newtonsoft.Json 9.0.1 版或更高版本安装 `Microsoft.Azure.DocumentDB.ChangeFeedProcessor`，并将其添加为引用。

创建一个监视、租用、目标集合 

若要使用更改源处理器库，需要在运行处理器主机之前创建租用集合。 再次建议将此租用集合存储在不同的帐户（写入区域更靠近更改源处理器运行位置）。 在此数据移动示例中，需要在运行更改源处理器主机前，创建目标集合。 在示例代码中，如果不存在监视、租用和目标集合，可调用一个帮助程序方法来创建这些集合。 

> [!WARNING]
> 创建集合会影响定价，因为要保留应用程序的吞吐量才能与 Azure Cosmos DB 进行通信。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

创建处理器主机

`ChangeFeedProcessorHost` 类为事件处理器实现提供线程安全、多进程安全的运行时环境，该环境还能提供检查点和分区租用管理。 若要使用 `ChangeFeedProcessorHost` 类，可实现 `IChangeFeedObserver`。 此接口包含三个方法：

* `OpenAsync`：打开更改源观察程序时，调用此函数。 使用者/观察程序打开时，可对其进行修改以执行特定操作。  
* `CloseAsync`：终止更改源观察程序时，调用此函数。 使用者/观察程序关闭时，可对其进行修改以执行特定操作。  
* `ProcessChangesAsync`：当文档的新更改在更改源上可用时，调用此函数。 更改源每次更新时，可对其进行修改以执行特定操作。  

在此示例中，通过 `DocumentFeedObserver` 类来实现接口 `IChangeFeedObserver`。 在此处，`ProcessChangesAsync` 函数将文档从更改源 upsert（更新）到目标集合。 如果要将数据从一个集合移动到另一个集合以更改数据集的分区键，此示例很有用。 

运行处理器主机

开始事件处理前，可自定义更改源选项和更改源主机选项。 
```csharp
    // Customizable change feed option and host options 
    ChangeFeedOptions feedOptions = new ChangeFeedOptions();

    // ie customize StartFromBeginning so change feed reads from beginning
    // can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
    feedOptions.StartFromBeginning = true;

    ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();

    // ie. customizing lease renewal interval to 15 seconds
    // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
    feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

```
下表中总结了可以自定义的特定字段。 

更改源选项：
<table>
    <tr>
        <th>属性名称</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>MaxItemCount</td>
        <td>在 Azure Cosmos DB 数据库服务中获取或设置枚举操作中将要返回的项的最大数目。</td>
    </tr>
    <tr>
        <td>PartitionKeyRangeId</td>
        <td>在 Azure Cosmos DB 数据库服务中获取或设置当前请求的分区键范围 ID。</td>
    </tr>
    <tr>
        <td>RequestContinuation</td>
        <td>在 Azure Cosmos DB 数据库服务中获取或设置请求继续标记。</td>
    </tr>
        <tr>
        <td>SessionToken</td>
        <td>在 Azure Cosmos DB 数据库服务中获取或设置用于会话一致性的会话标记。</td>
    </tr>
        <tr>
        <td>StartFromBeginning</td>
        <td>获取或设置在 Azure Cosmos DB 数据库服务中，更改源是应从头开始 (true)，还是应从当前位置开始 (false)。 默认设置为从当前位置 (false) 开始。</td>
    </tr>
</table>

更改源主机选项：
<table>
    <tr>
        <th>属性名称</th>
        <th>类型</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>LeaseRenewInterval</td>
        <td>TimeSpan</td>
        <td>当前由 ChangeFeedEventHost 实例拥有的分区的所有租用的间隔。</td>
    </tr>
    <tr>
        <td>LeaseAcquireInterval</td>
        <td>TimeSpan</td>
        <td>开始执行用于计算分区是否在已知主机实例中均匀分布的任务的间隔。</td>
    </tr>
    <tr>
        <td>LeaseExpirationInterval</td>
        <td>TimeSpan</td>
        <td>表示分区的租用上采用租用的间隔。 如果在此间隔内不续订租用，则该租用过期，分区的所有权会移到另一个 ChangeFeedEventHost 实例。</td>
    </tr>
    <tr>
        <td>FeedPollDelay</td>
        <td>TimeSpan</td>
        <td>所有当前更改均耗尽后，源上新更改的分区轮询间的延迟。</td>
    </tr>
    <tr>
        <td>CheckpointFrequency</td>
        <td>CheckpointFrequency</td>
        <td>检查点租用频率。</td>
    </tr>
    <tr>
        <td>MinPartitionCount</td>
        <td>int</td>
        <td>主机最小分区计数。</td>
    </tr>
    <tr>
        <td>MaxPartitionCount</td>
        <td>int</td>
        <td>主机可提供的最大分区数。</td>
    </tr>
    <tr>
        <td>DiscardExistingLeases</td>
        <td>Bool</td>
        <td>是否应在主机开始时删除所有现有的租用，使主机从头开始。</td>
    </tr>
</table>


若要开始处理事件，请实例化 `ChangeFeedProcessorHost`，这会为 Azure Cosmos DB 集合提供适当的参数。 然后，使用运行时调用 `RegisterObserverAsync` 注册 `IChangeFeedObserver`（此示例中的 DocumentFeedObserver）实现。 此时，主机将尝试使用“贪婪”算法在 Azure Cosmos DB 集合中的每个分区键范围上获取租用。 这些租用只在指定的时间段内有效，之后必须续订。 当新节点（本例中的辅助角色实例）进入联机状态时，它们将进行租约预订，当每个主机尝试获取更多租约时，负载会在节点之间转移。 

在示例代码中，使用工厂类 (DocumentFeedObserverFactory.cs) 创建观察者和 `RegistObserverFactoryAsync`，以注册观察者。 

```csharp
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
    {
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);

        await host.RegisterObserverFactoryAsync(docObserverFactory);

        Console.WriteLine("Running... Press enter to stop.");
        Console.ReadLine();

        await host.UnregisterObserversAsync();
    }
```
经过一段时间后，就会建立平衡。 这种动态功能使基于 CPU 的自动缩放能应用于使用者，以进行扩展和缩小。 如果 Azure Cosmos DB 中的更改提供速率超过了使用者可以处理的速率，则可使用使用者的 CPU 增大功能来实现辅助角色实例数的自动缩放。

## <a name="next-steps"></a>后续步骤
* 试用 [GitHub 上的 Azure Cosmos DB 更改源代码示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* 使用 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 或 [REST API](/rest/api/documentdb/) 开始编写代码。

