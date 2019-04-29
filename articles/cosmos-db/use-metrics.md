---
title: 使用 Azure Cosmos DB 中的指标进行监视和调试
description: 使用 Azure Cosmos DB 中的指标调试常见问题和监视数据库。
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.reviewer: sngun
ms.openlocfilehash: ff6e0b6084eebf236d01b4dd00a46897687938c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765780"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的指标进行监视和调试

Azure Cosmos DB 提供吞吐量、存储、一致性、可用性和延迟的指标。 [Azure 门户](https://portal.azure.com)提供这些指标的聚合视图。 如需更精细的指标，可使用客户端 SDK 和[诊断日志](./logging.md)。

本文介绍常见用例，以及如何使用 Azure Cosmos DB 指标来分析和调试这些问题。 指标每 5 分钟收集一次且保留 7 天。

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>了解成功的请求数或导致错误的请求数

若要开始，请前往 [Azure 门户](https://portal.azure.com)并导航到“指标”边栏选项卡。 在此边栏选项卡查找“每分钟超出容量的请求数”图表。 该图表显示按状态代码划分的总请求数（以分钟计）。 有关 HTTP 状态代码的详细信息，请参阅 [Azure Cosmos DB 的 HTTP 状态代码](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

最常见的错误状态代码为 429（速率限制）。 此错误意味着对 Azure Cosmos DB 的请求超过预配的吞吐量。 此问题最常见的解决方案是为给定集合[纵向扩展 RU](./set-throughput.md)。

![每分钟的请求数](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>确定跨分区的吞吐量分布

对任何可伸缩应用程序而言，均必须具有良好的分区键基数。 若要确定任何由分区细分为分区容器的吞吐量分布，请导航到 [Azure 门户](https://portal.azure.com)中的“指标”边栏选项卡。 在“吞吐量”选项卡中，存储细目显示在“各物理分区占用的最大 RU 数/秒”图表中。 下图显示一个示例介绍因最左侧的倾斜分区而产生的不良数据分布。

![单个分区在下午 3:05 的使用率很高](media/use-metrics/metrics-17.png)

吞吐量分布不均可能导致热分区，进而造成请求受阻和需要重新分区。 若要深入了解如何在 Azure Cosmos DB 中进行分区，请参阅[在 Azure Cosmos DB 中进行分区和缩放](./partition-data.md)。

## <a name="determine-the-storage-distribution-across-partitions"></a>确定跨分区的存储分布

对任何可伸缩应用程序而言，均必须具有良好的分区基数。 若要确定任何按分区细分为分区容器的存储分布，请前往 [Azure 门户](https://portal.azure.com)中的“指标”边栏选项卡。 在“存储”选项卡中，存储细分显示在顶部分区键图表所占用的“数据 + 索引”存储中。 下图说明了数据存储的不良分布，如最左侧的倾斜分区所示。

![不良数据分布示例](media/use-metrics/metrics-07.png)

可单击图表上的分区，深入查看当前造成分布倾斜的分区键。

![分区键使分布倾斜](media/use-metrics/metrics-05.png)

确定导致分布倾斜的分区键之后，可能需使用进一步分布的分区键重新执行容器分区。 若要深入了解如何在 Azure Cosmos DB 中进行分区，请参阅[在 Azure Cosmos DB 中进行分区和缩放](./partition-data.md)。

## <a name="compare-data-size-against-index-size"></a>比较数据与索引的大小

在 Azure Cosmos DB 中，所用存储空间总量是指数据大小和索引大小的总和。 索引大小通常只是数据大小的一小部分。 在 [Azure 门户](https://portal.azure.com)的“指标”边栏选项卡，“存储”选项卡显示基于数据和索引的存储空间使用量详情。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

若要节省索引空间，可调整[索引策略](index-policy.md)。

## <a name="debug-why-queries-are-running-slow"></a>调试查询运行缓慢的原因

在 SQL API SDK 中，Azure Cosmos DB 提供查询执行的统计信息。

```csharp
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

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

QueryMetrics 提供执行各查询组件所用时长的详细信息。 导致查询长时间运行的最常见根因是扫描，这意味着查询无法利用索引。 可通过设置更好的筛选条件来解决此问题。

## <a name="next-steps"></a>后续步骤

前文介绍了如何使用 Azure 门户中提供的指标来监视和调试问题。 以下文章进一步介绍了如何提高数据库性能：

* [执行 Azure Cosmos DB 缩放和性能测试](performance-testing.md)
* [Azure Cosmos DB 性能提示](performance-tips.md)
