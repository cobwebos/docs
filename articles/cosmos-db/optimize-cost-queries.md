---
title: 优化 Azure Cosmos DB 中运行查询的请求单位和成本
description: 了解如何评估查询的请求单位费用，并在查询性能和成本方面优化查询。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: rimman
ms.openlocfilehash: bdf223e60015c4e5d96416f95c410854a057c02c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717006"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>优化 Azure Cosmos DB 中的查询成本

Azure Cosmos DB 提供了丰富的数据库操作，包括对容器中的项进行操作的关系和分层查询。 与这些操作关联的成本取决于完成操作所需的 CPU、IO 和内存。 可以考虑将请求单位 (RU) 视为执行各种数据库操作以提供请求所需的资源的单一度量值，而无需虑和管理硬件资源。 本文介绍如何评估查询的请求单位费用，并在性能和成本方面优化查询。 

Azure Cosmos DB 中的查询通常按吞吐量从最快/最高效到较慢/效率较低进行排序，如下所示：  

* 针对单个分区键和项键的 GET 操作。

* 单个分区键内具有筛选器子句的查询。

* 针对任何属性都没有等于或范围筛选器子句的查询。

* 不包含筛选器的查询。

从一个或多个分区读取数据的查询会导致较高的延迟并使用较多的请求单位数。 因为每个分区都具有针对所有属性的自动索引编制，因此，可以基于索引高效地执行查询。 通过使用并行选项，可以更快地进行使用多个分区的查询。 若要了解有关分区和分区键的详细信息，请参阅[在 Azure Cosmos DB 中进行分区](partitioning-overview.md)。

## <a name="evaluate-request-unit-charge-for-a-query"></a>评估查询的请求单位费用

将一些数据存储在 Azure Cosmos 容器中后，可以使用 Azure 门户中的数据资源管理器来构建和运行查询。 此外可以通过使用数据资源管理器获取查询的成本。 此方法使你了解系统支持的典型查询和操作所涉及的实际费用。

此外可以使用 SDK 以编程方式获取查询的成本。 要测量任何操作（如创建、更新或删除）的开销，请在使用 REST API 时检查 `x-ms-request-charge` 标头。 如果使用的是 .NET 或 Java SDK，则 `RequestCharge` 属性是获取请求费用的等效属性，并且此属性存在于 ResourceResponse 或 FeedResponse 中。

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>影响查询请求单位费用的因素

查询的请求单位依赖于许多因素。 例如，加载/返回的 Azure Cosmos 项的数量、对索引的查找次数、查询编译时间等详细信息。 Azure Cosmos DB 保证在相同数据上执行相同的查询时，即使重复执行，也始终使用相同数量的请求单位。 使用查询执行指标的查询配置文件使你可以很好地了解请求单位的使用情况。  

在某些情况下，可能会在查询的分页执行中看到 200 个和 429 个响应序列以及变量请求单位，这是因为查询将根据可用的 RU 尽可能快地运行。 可能会看到查询执行在服务器和客户端之间分成多个页面/往返。 例如，10,000 个项可以作为多个页面返回，每个页面根据对该页面执行的计算收费。 对这些页面求和时，应获得与整个查询相同的 RU 数。  

## <a name="metrics-for-troubleshooting"></a>故障排除的指标

查询、用户定义的函数 (UDF) 所使用的性能和吞吐量主要取决于函数本身。 查找 UDF 中查询执行花费的时间和使用的 RU 数量的最简单方法是启用查询指标。 如果使用的是 .NET SDK，则以下是 SDK 返回的示例查询指标：

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>成本优化查询的最佳做法 

优化成本查询时，请考虑以下最佳做法：

* **共置多个实体类型**

   尝试在单个或较少数量的容器中共置多个实体类型。 此方法不仅对定价有好处，而且对查询执行和事务也有好处。 查询的作用域为单个容器；通过存储过程/触发器的多个记录的原子事务的作用域为单个容器内的分区键。 在同一容器内共置实体可以减少网络往返次数，以便解析记录之间的关系。 因此，它可以提高端到端性能，为较大的数据集启用多个记录的原子事务，从而降低成本。 如果你的情景难以在单个或较少数量的容器中共置多个实体类型，通常是因为你正在迁移现有应用程序且不希望进行任何代码更改 - 你应该考虑在数据库级别预配吞吐量。  

* **测量和优化较低的每秒请求单位使用量**

   查询的复杂性会影响操作使用的请求单位 (RU)数量。 谓词数、谓词性质、UDF 数目以及源数据集的大小。 所有这些因素都会影响查询操作的成本。 

   请求标头中返回的请求费用表示给定查询的费用。 例如，如果查询返回 1000 个 1 KB 项，则操作成本为 1000。 因此在一秒内，服务器在对后续请求进行速率限制之前，只接受两个此类请求。 有关详细信息，请参阅[请求单位](request-units.md)一文和请求单位计算器。 

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解 [Azure Cosmos 定价的原理](how-pricing-works.md)
* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解[优化多区域 Azure Cosmos 帐户的成本](optimize-cost-regions.md)
* 详细了解 [Azure Cosmos DB 保留容量](cosmos-db-reserved-capacity.md)

