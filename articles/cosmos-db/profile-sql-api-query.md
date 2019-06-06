---
title: 获取 SQL 查询性能和执行度量值
description: 了解如何检索 SQL 查询执行指标和配置文件的 Azure Cosmos DB 请求的 SQL 查询性能。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: b4017666956d0e01ea19781fb4f1ce2dde15fff5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481559"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>获取 SQL 查询执行指标和分析查询性能，使用.NET SDK

本文介绍如何分析 Azure Cosmos DB 上的 SQL 查询性能。 完成此分析使用`QueryMetrics`从.NET SDK 检索，并且此处详细介绍。 [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx)是后端查询执行有关的信息的强类型的对象。 中更详细地阐述了这些指标[优化查询性能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文。

## <a name="set-the-feedoptions-parameter"></a>设置 FeedOptions 参数

有关的所有重载[DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx)采用一个可选[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)参数。 此选项，可以进行调整和参数化查询执行。 

若要收集的 Sql 查询执行指标，必须设置参数[PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics)中[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx)到`true`。 设置`PopulateQueryMetrics`为 true 将使它，以便`FeedResponse`将包含相关`QueryMetrics`。 

## <a name="get-query-metrics-with-asdocumentquery"></a>获取与 AsDocumentQuery() 查询度量值
下面的代码示例演示如何使用时是否检索度量值[AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx)方法：

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

在上一节中注意到没有对多个调用[ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx)方法。 每次调用返回`FeedResponse`具有的字典对象`QueryMetrics`; 一个用于查询的每个延续任务。 下面的示例演示如何聚合这些`QueryMetrics`使用 LINQ:

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

## <a name="grouping-query-metrics-by-partition-id"></a>对查询指标按分区 ID 进行分组

可以分组`QueryMetrics`由分区 id。 按分区 ID 进行分组，可了解特定分区是否导致性能问题时与其他。 下面的示例演示如何进行分组`QueryMetrics`与 linq 结合使用：

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

## <a name="linq-on-documentquery"></a>LINQ DocumentQuery 上

此外可以获取`FeedResponse`从 LINQ 查询使用`AsDocumentQuery()`方法：

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>消耗资源的查询

您可以捕获由每个查询的请求单位数来调查消耗资源的查询或使用高吞吐量的查询。 可以使用获取的请求费用[RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx)属性中的`FeedResponse`。 若要了解有关如何获取使用 Azure 门户和不同的 Sdk 的请求费用的详细信息，请参阅[找到的请求单位费用](find-request-unit-charge.md)一文。

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

在计算时执行的客户端的查询所需的时间，请确保仅包含要调用的时间`ExecuteNextAsync`方法并不基本代码的其他部分。 只是这些调用将帮助你在计算查询执行时间，在下面的示例所示：

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

## <a name="scan-queries-commonly-slow-and-expensive"></a>扫描查询 （通常慢速且昂贵）

扫描查询是指不由提供的索引，因而，很多的查询返回的结果集之前加载文档。

下面是扫描查询的示例：

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查询的筛选器使用系统函数右上角，从该索引并不提供。 执行此查询针对大型集合生成第一个延续的以下查询指标：

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

此查询加载 60,951 文档，总和 399,998,938 字节。 正在加载这么多字节会导致高成本或请求单位费用。 它还采用较长时间才能执行查询，这是清除与所用的总时间属性：

```
Total Query Execution Time               :        4,500.34 milliseconds
```

这意味着该查询用了 4.5 长时间执行 （并且这是只有一个继续符）。

若要优化此示例查询，避免使用筛选器中的上限。 相反，文档会创建或更新时，`c.description`必须在所有大写字符插入值。 该查询然后将变为： 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

此查询现在就可通过索引进行处理。

若要了解有关优化查询性能的详细信息，请参阅[优化查询性能](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)一文。

## <a id="References"></a>参考

- [Azure Cosmos DB SQL 规范](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>后续步骤

- [优化查询性能](sql-api-query-metrics.md)
- [索引概述](index-overview.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
