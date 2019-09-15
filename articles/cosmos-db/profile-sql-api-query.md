---
title: 获取 SQL 查询性能和执行指标
description: 了解如何检索 SQL 查询执行指标并分析 Azure Cosmos DB 请求的 SQL 查询性能。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998368"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>使用 .NET SDK 获取 SQL 查询执行指标并分析查询性能

本文介绍如何分析 Azure Cosmos DB 中的 SQL 查询性能。 可以使用从 .NET SDK 检索到且在本文中详述的 `QueryMetrics` 来执行这种分析。 [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) 是一个强类型化对象，包含有关后端查询执行的信息。 [优化查询性能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文中更详细地阐述了这些指标。

## <a name="set-the-feedoptions-parameter"></a>设置 FeedOptions 参数

[DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) 的所有重载采用可选的 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 参数。 此选项涉及到如何优化和参数化查询执行。 

若要收集 SQL 查询执行指标，必须将 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) 中的参数 [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) 设置为 `true`。 如果将 `PopulateQueryMetrics` 设置为 true，`FeedResponse` 将包含相关的 `QueryMetrics`。 

## <a name="get-query-metrics-with-asdocumentquery"></a>使用 AsDocumentQuery() 获取查询指标
以下代码示例演示使用 [AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) 方法时如何检索指标：

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>聚合 QueryMetrics

上一部分提到，已多次调用 [ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) 方法。 每次调用都会返回一个包含 `QueryMetrics` 字典的 `FeedResponse` 对象；每次延续查询都会返回一个此类对象。 以下示例演示如何使用 LINQ 聚合这些 `QueryMetrics`：

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>按分区 ID 将查询指标分组

可按分区 ID 将 `QueryMetrics` 分组。 按分区 ID 分组可将特定的分区与其他分区进行比较，以此判断该分区是否导致性能问题。 以下示例演示如何使用 LINQ 将 `QueryMetrics` 分组：

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ on DocumentQuery

还可以使用 `AsDocumentQuery()` 方法从 LINQ 查询中获取 `FeedResponse`：

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>高开销的查询

可以捕获每个查询消耗的请求单位数，以调查高开销的查询，或者消耗了大量吞吐量的查询。 可以使用 `FeedResponse` 中的 [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) 属性获取请求费用。 若要详细了解如何使用 Azure 门户和不同的 SDK 获取请求费用，请参阅[查找请求单位费用](find-request-unit-charge.md)一文。

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>获取查询执行时间

计算执行客户端查询所需的时间时，请确保仅包含调用 `ExecuteNextAsync` 方法所需的时间，而不要包含基代码的其他部分。 只是考虑这些调用就能帮助你计算查询执行所需的时间，如以下示例所示：

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>扫描查询（通常速度缓慢且开销较高）

扫描查询是指不由索引提供服务的查询，因此，在返回结果集之前需要加载许多文档。

下面是扫描查询的一个示例：

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查询的筛选器使用系统函数 UPPER，该函数不是由索引提供服务。 针对大型集合执行此查询在首次延续时生成了以下查询指标：

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

请注意查询指标输出中的以下值：

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

此查询加载了 60,951 个文档，总共为 399,998,938 字节。 加载这么多的字节会导致开销或请求单位费用增大。 它还花费了较长时间来执行查询，花费在属性上的明确总时间为：

```
Total Query Execution Time               :        4,500.34 milliseconds
```

这意味着，执行该查询花费了 4.5 秒（而且这只是第一次延续）。

若要优化此示例查询，请避免在筛选器中使用 UPPER。 在创建或更新文档时，必须插入全大写的 `c.description` 值。 然后，该查询将变成： 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

现在，可以从索引为此查询提供服务。

若要详细了解如何优化查询性能，请参阅[优化查询性能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文。

## <a id="References"></a>参考

- [Azure Cosmos DB SQL 规范](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>后续步骤

- [优化查询性能](sql-api-query-metrics.md)
- [索引概述](index-overview.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
