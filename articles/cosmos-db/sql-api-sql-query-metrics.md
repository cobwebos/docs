---
title: Azure Cosmos DB SQL API 的 SQL 查询指标 | Microsoft Docs
description: 了解如何检测和调试 Azure Cosmos DB 请求的 SQL 查询性能。
keywords: sql 语法, sql 查询, sql 查询, json 查询语言, 数据库概念和 sql 查询, 聚合函数
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: 4ed0008f4b574691387d6e0ee0300b5f05f1ec1b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798689"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>优化 Azure Cosmos DB 的查询性能

Azure Cosmos DB 提供了一个[用于查询数据的 SQL API](sql-api-sql-query.md)，不需要使用架构或辅助索引。 本文为开发者提供了以下信息：

* 有关 Azure Cosmos DB 的 SQL 查询执行如何工作的概要详细信息
* 有关查询请求和响应标头以及客户端 SDK 选项的详细信息
* 有关查询性能的提示和最佳做法
* 有关如何使用 SQL 执行统计信息调试查询性能的示例

## <a name="about-sql-query-execution"></a>关于 SQL 查询执行

在 Azure Cosmos DB 中，数据存储在容器中，容器可以增长到任何[存储大小或请求吞吐量](partition-data.md)。 Azure Cosmos DB 在幕后无缝地在物理分区之间缩放数据来以预配的吞吐量处理数据增长。 可以使用 REST API 或受支持的 [SQL SDK](sql-api-sdk-dotnet.md) 之一向任何容器发出 SQL 查询。

分区的简要概述：定义一个分区键（例如“city”），它决定了如何在物理分区之间拆分数据。 属于单个分区键（例如 "city" == "Seattle"）的数据存储在一个物理分区中，但单个物理分区通常具有多个分区键。 当某个分区达到其存储大小时，服务会无缝地将分区拆分为两个新分区，并且会将分区键平均分割到这些分区中。 因为分区是暂时的，因此，API 使用“分区键范围”的抽象，它表示分区键哈希的范围。 

当向 Azure Cosmos DB 发出查询时，SDK 执行以下逻辑步骤：

* 分析 SQL 查询来确定查询执行计划。 
* 如果查询包括一个针对分区键的筛选器，例如 `SELECT * FROM c WHERE c.city = "Seattle"`，则它被传送到单个分区。 如果查询没有针对分区键的筛选器，则会在所有分区中执行该查询，并且结果是合并的客户端。
* 查询将根据客户端配置在每个分区内串行或并行执行。 在每个分区内，查询可能会进行一次或多次往返，具体取决于查询复杂性、所配置的页面大小和预配的集合吞吐量。 每个执行都返回由查询执行使用的[请求单位](request-units.md)数以及可选的查询执行统计信息。 
* SDK 对跨分区的查询结果进行汇总。 例如，如果查询涉及跨分区的 ORDER BY，则会对来自各个分区的结果进行合并排序以返回全局排序的结果。 如果查询是类似于 `COUNT` 的聚合，则会对来自各个分区的计数进行求和以生成总数。

SDK 针对查询执行提供了各种选项。 例如，在 .NET 中，`FeedOptions` 类中提供了以下选项。 下表介绍了这些选项以及它们如何影响查询执行时间。 

| 选项 | 说明 |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | 对于需要跨多个分区执行的任何查询，都必须将其设置为 true。 这是一个显式标志，可用来在开发时有意识地进行性能权衡。 |
| `EnableScanInQuery` | 如果已决定不使用索引编制，但仍然希望通过扫描方式运行查询，必须将其设置为 true。 只有针对所请求的筛选器路径禁用了索引编制时才适用。 | 
| `MaxItemCount` | 到服务器的每次往返要返回的最大项数。 通过将其设置为 -1，可以让服务器来管理此项数。 或者，可以减小此值来使每次往返仅检索少量项。 
| `MaxBufferedItemCount` | 这是一个客户端选项，在执行跨分区 ORDER BY 时用来限制内存占用。 较高的值有助于降低跨分区排序的延迟。 |
| `MaxDegreeOfParallelism` | 获取或设置在 Azure Cosmos DB 数据库服务中并行执行查询期间客户端运行的并发操作数。 属性值为正会将并发操作数限制为所设置的值。 如果它设置为小于 0，则系统会自动决定要运行的并发操作数。 |
| `PopulateQueryMetrics` | 详细记录在执行查询的各个阶段花费的时间的统计信息，例如编译时间、索引循环时间和文档加载时间。 可以与 Azure 支持共享来自查询统计信息的输出以诊断查询性能问题。 |
| `RequestContinuation` | 可以通过传入任何查询返回的不透明继续标记来继续执行查询。 继续标记封装了执行查询所需的所有状态。 |
| `ResponseContinuationTokenLimitInKb` | 可以限制服务器返回的继续标记的最大大小。 如果应用程序主机对响应标头大小有限制，则可能需要设置此项。 设置此项可能会增加查询的总体持续时间和所使用的 RU。  |

例如，让我们以针对在某个集合上请求的分区键的查询为例，该集合以 `/city` 作为分区键并且预配的吞吐量为 100,000 RU/s。 你使用 .NET 中的 `CreateDocumentQuery<T>` 请求执行此查询，如下所示：

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

上面显示的 SDK 片段对应于以下 REST API 请求：

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

每个查询执行页面对应于一个标头为 `Accept: application/query+json` 的 REST API `POST`，SQL 查询在正文中。 每个查询都使用为了继续执行而在客户端与服务器之间回应的 `x-ms-continuation` 标记进行到服务器的一次或多次往返。 FeedOptions 中的配置选项将以请求标头的形式传递给服务器。 例如，`MaxItemCount` 对应于 `x-ms-max-item-count`。 

该请求返回以下响应（为便于阅读已将其截断）：

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

从查询返回的主要响应标头包括以下内容：

| 选项 | 说明 |
| ------ | ----------- |
| `x-ms-item-count` | 响应中返回的项数。 这取决于所提供的 `x-ms-max-item-count`、在最大响应有效负载大小内可以容纳的项数、预配的吞吐量以及查询执行时间。 |  
| `x-ms-continuation:` | 用于继续执行查询的继续标记（如果有更多结果）。 | 
| `x-ms-documentdb-query-metrics` | 执行的查询统计信息。 这是一个经分隔的字符串，其中包含在执行查询的各个阶段中花费的时间的统计信息。 如果 `x-ms-documentdb-populatequerymetrics` 设置为 `True`，则会返回。 | 
| `x-ms-request-charge` | 查询使用的[请求单位](request-units.md)数。 | 

有关 REST API 请求标头和选项的详细信息，请参阅[使用 REST API 查询资源](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api)。

## <a name="best-practices-for-query-performance"></a>有关查询性能的最佳做法
下面是影响 Azure Cosmos DB 查询性能的最常见因素。 本文深入探讨了其中的每一个主题。

| 因素 | 提示 | 
| ------ | -----| 
| 预配的吞吐量 | 度量每个查询的 RU，并确保你具有查询所需的预配吞吐量。 | 
| 分区和分区键 | 支持在查询的筛选器子句中使用分区键值以降低延迟。 |
| SDK 和查询选项 | 遵循 SDK 最佳做法（例如直接连接）并优化客户端查询执行选项。 |
| 网络延迟 | 在度量时考虑网络开销，并使用多宿主 API 从最近的区域进行读取。 |
| 索引策略 | 确保具有查询所需的索引路径/策略。 |
| 查询执行指标 | 对查询执行指标进行分析来查明潜在的查询和数据形状重写。  |

### <a name="provisioned-throughput"></a>预配的吞吐量
在 Cosmos DB 中，创建数据容器，每个容器都具有以每秒请求单位 (RU) 表示的预留吞吐量。 读取 1-KB 文档为 1 个 RU，每个操作（包括查询）都根据其复杂性规范化为固定数量的 RU。 例如，如果你为容器预配了 1000 RU/s，并且你具有使用 5 个 RU 的类似于 `SELECT * FROM c WHERE c.city = 'Seattle'` 的查询，则每秒可以执行200 个这样的查询（(1000 RU/s) / (5 RU/查询) = 200 查询/s）。 

如果你每秒提交的查询多于 200 个，则服务会对高于 200/s 的传入请求进行速率限制。 SDK 会通过执行回退/重试自动处理此情况，因此你可能会注意到这些查询有较高的延迟。 将预配的吞吐量提高到所需的值可以改进查询延迟和吞吐量。 

若要了解请求单位的详细信息，请参阅[请求单位](request-units.md)。

### <a name="partitioning-and-partition-keys"></a>分区和分区键
使用 Azure Cosmos DB 时，查询通常采用以下顺序按从最快/最高效到较慢/较低效的顺序执行。 

* 针对单个分区键和项键的 GET
* 具有针对单个分区键的筛选器子句的查询
* 针对任何属性都没有等于或范围筛选器子句的查询
* 没有筛选器的查询

需要查阅所有分区的查询需要较高的延迟，并且会使用较高的 RU。 因为每个分区都具有针对所有属性的自动索引编制功能，因此，在这种情况下，可以基于索引高效地执行查询。 可以通过使用并行度选项使跨分区的查询更快地执行。

若要了解有关分区和分区键的详细信息，请参阅[在 Azure Cosmos DB 中进行分区](partition-data.md)。

### <a name="sdk-and-query-options"></a>SDK 和查询选项
请参阅[性能提示](performance-tips.md)和[性能测试](performance-testing.md)来了解如何从 Azure Cosmos DB 获得最佳客户端性能。 这包括使用最新的 SDK、配置特定于平台的配置（例如默认连接数、垃圾收集频率）以及使用诸如直连/TCP 之类的轻型连接。 


#### <a name="max-item-count"></a>最大项计数
对查询而言，`MaxItemCount` 的值对端到端查询时间具有显著影响。 服务器的每次往返所返回的项数不超过 `MaxItemCount` 中的数目（默认为 100 个项）。 将此值设为更大值（-1 为最大值，推荐使用此值），通过限制服务器和客户端之间的往返数（尤其针对具有大型结果集的查询）可提高总体查询持续时间。

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>最大并行度
对于查询，优化 `MaxDegreeOfParallelism` 来确定适合你的应用程序的最佳配置，尤其是当执行跨分区查询时（没有针对分区键值的筛选器）。 `MaxDegreeOfParallelism` 控制并行任务的最大数目，即要并行访问的最大分区数。 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

假设
* D = 默认的最大并行任务数（客户端计算机中处理器的总数）
* P = 用户定义的最大并行任务数
* N = 为响应查询需要访问的分区数

以下为 P 值不同时并行查询行为的影响。
* (P == 0) => 串行模式
* (P == 1) => 一个任务的最大数
* (P > 1) => Min (P, N) 并行任务数 
* (P < 1) => Min (N, D) 并行任务数

有关 SDK 发行说明和已实现的类和方法的详细信息，请参阅 [SQL SDK](sql-api-sdk-dotnet.md)

### <a name="network-latency"></a>网络延迟
请参阅 [Azure Cosmos DB 全局分发](tutorial-global-distribution-sql-api.md)来了解如何设置全局分发以及如何连接到最近到区域。 当需要进行多次往返或需要通过查询检索大型结果集时，网络延迟对查询性能有显著影响。 

有关查询执行指标的部分介绍如何检索查询的服务器执行时间 ( `totalExecutionTimeInMs`)，以便可区分查询执行和网络传输所用的时间。

### <a name="indexing-policy"></a>索引编制策略
若要了解索引编制路径、种类和模式以及它们对查询执行有何影响，请参阅[配置索引编制策略](indexing-policies.md)。 默认情况下，索引编制策略为字符串使用哈希索引编制，字符串比较适合进行等式查询，但不适合进行范围查询/order by 查询。 如果需要对字符串使用范围查询，建议为所有字符串指定范围索引类型。 

## <a name="query-execution-metrics"></a>查询执行指标
可以通过传入可选的 `x-ms-documentdb-populatequerymetrics` 标头（在 .NET SDK 中为 `FeedOptions.PopulateQueryMetrics`）获取有关查询执行的详细指标。 `x-ms-documentdb-query-metrics` 中返回的值具有适用于对查询执行进行高级故障排除的以下键-值对。 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| 指标 | 单位 | 说明 | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | 毫秒 | 查询执行时间 | 
| `queryCompileTimeInMs` | 毫秒 | 查询编译时间  | 
| `queryLogicalPlanBuildTimeInMs` | 毫秒 | 生成逻辑查询计划的时间 | 
| `queryPhysicalPlanBuildTimeInMs` | 毫秒 | 生成物理查询计划的时间 | 
| `queryOptimizationTimeInMs` | 毫秒 | 优化查询时花费的时间 | 
| `VMExecutionTimeInMs` | 毫秒 | 查询运行时花费的时间 | 
| `indexLookupTimeInMs` | 毫秒 | 在物理索引层中花费的时间 | 
| `documentLoadTimeInMs` | 毫秒 | 加载文档时花费的时间  | 
| `systemFunctionExecuteTimeInMs` | 毫秒 | 执行系统（内置）函数花费的总时间（毫秒）  | 
| `userFunctionExecuteTimeInMs` | 毫秒 | 执行用户定义的函数花费的总时间（毫秒） | 
| `retrievedDocumentCount` | 计数 | 检索的文档总数  | 
| `retrievedDocumentSize` | 字节 | 检索的文档的总大小（字节）  | 
| `outputDocumentCount` | 计数 | 输出文档数 | 
| `writeOutputTimeInMs` | 毫秒 | 查询执行时间（毫秒） | 
| `indexUtilizationRatio` | 比率 (<=1) | 由筛选器匹配出的文档数与加载的文档数的比率  | 

客户端 SDK 可以在内部执行多个查询操作来为每个分区中的查询提供服务。 如果总结果数超出了 `x-ms-max-item-count`、查询超出了分区的预配吞吐量，或者查询有效负载达到了每页最大大小或查询达到了系统分配的超时限制，则客户端会对每个分区进行多次调用。 每一部分查询执行都会为该页返回一个 `x-ms-documentdb-query-metrics`。 

下面提供了一些示例查询，并说明了如何解释从查询执行返回的某些指标： 

| 查询 | 示例指标 | 说明 | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | 为匹配 TOP 子句而检索的文档数为 100+1。 查询时间主要花费在 `WriteOutputTime` 和 `DocumentLoadTime` 中，因为它是一个扫描。 | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount 现在较高（为匹配 TOP 子句为 500+1）。 | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | IndexLookupTime 中花费的用于查找键的时间大约为 0.9 毫秒，因为它是针对 `/N/?` 的索引查找。 | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | IndexLookupTime 中花费的时间（1.7 毫秒）稍高于范围扫描，因为它是针对 `/N/?` 的索引查找。 | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | 在 `DocumentLoadTime` 上花费的时间与前面的查询相同，但 `WriteOutputTime` 较低，因为我们仅对一个属性进行了投影。 | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | `UserDefinedFunctionExecutionTime` 中对 `c.N` 的每个值执行 UDF 时花费的时间大约为 213 毫秒。 |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | `IndexLookupTime` 中在 `/Name/?` 上花费的时间大约为 0.6 毫秒。 大多数查询执行时间花费在了 `SystemFunctionExecutionTime` 中（~7 毫秒）。 |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | 查询是作为扫描执行的，因为它使用了 `LOWER`，并且返回了所检索的 2491 个文档中的 500 个。 |


## <a name="next-steps"></a>后续步骤
* 若要了解受支持的 SQL 查询运算符和关键字，请参阅 [SQL 查询](sql-api-sql-query.md)。 
* 若要了解请求单位，请参阅[请求单位](request-units.md)。
* 若要了解索引编制策略，请参阅[索引编制策略](indexing-policies.md) 


