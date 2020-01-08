---
title: 使用 Azure Cosmos DB 排查查询问题
description: 了解如何识别、诊断 Azure Cosmos DB SQL 查询问题并对其进行故障排除。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 1859fa8f71b5c4c44d6e5da1b6a36ca9d9399516
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444719"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB 的查询性能故障排除
本文介绍如何识别、诊断和解决 Azure Cosmos DB SQL 查询问题。 若要为 Azure Cosmos DB 查询获得最佳性能，请遵循以下故障排除步骤。 

## <a name="collocate-clients-in-same-azure-region"></a>在同一 Azure 区域中归置客户端 
通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/#services)一文。

## <a name="check-consistency-level"></a>检查一致性级别
[一致性级别](consistency-levels.md)会影响性能和费用。 请确保一致性级别适用于给定方案。 有关更多详细信息，请参阅[选择一致性级别](consistency-levels-choosing.md)。

## <a name="log-the-executed-sql-query"></a>记录已执行的 SQL 查询 

可以在存储帐户或诊断日志表中记录已执行的 SQL 查询。 [利用诊断日志中的 SQL 查询日志](cosmosdb-monitor-resource-logs.md)，可以在所选的存储帐户中记录模糊查询。 这使你可以查看使用更多 ru 的日志和查找查询。 稍后，您可以使用活动 ID 来匹配 QueryRuntimeStatistics 中的实际查询。 对于安全用途和查询参数名称，将对查询进行模糊处理，其中，where 子句中的值与实际名称和值不同。 你可以使用日志记录到存储帐户来保留所执行查询的长期保留。  

## <a name="log-query-metrics"></a>日志查询指标

使用 `QueryMetrics` 排查速度缓慢或开销较高的查询。 

  * 将 `FeedOptions.PopulateQueryMetrics = true` 设置为在响应中 `QueryMetrics`。
  * `QueryMetrics` 类具有一个重载 `.ToString()` 函数，可以调用该函数以获取 `QueryMetrics`的字符串表示形式。 
  * 可以利用这些指标来派生以下见解： 
  
      * 查询管道的任何特定组件是否需要很长时间才能完成（按数百毫秒或更长的顺序）。 

          * 查看 `TotalExecutionTime`。
          * 如果查询的 `TotalExecutionTime` 小于结束执行时间，则将在客户端或网络中花费时间。 仔细检查客户端和 Azure 区域是否已并置。
      
      * 分析的文档中是否有误报（如果输出文档计数比检索到的文档数少很多）。  

          * 查看 `Index Utilization`。
          * `Index Utilization` = （返回的文档数/已加载的文档数）
          * 如果返回的文档数比加载的数量少很多，则将分析误报。
          * 用较窄的筛选器限制检索的文档数。  

      * 单个往返行程 fared 的方式（请参阅 `QueryMetrics`的字符串表示形式中的 `Partition Execution Timeline`）。 
      * 查询是否消耗高请求费用。 

有关更多详细信息，请参阅[如何获取 SQL 查询执行指标](profile-sql-api-query.md)一文。
      
## <a name="tune-query-feed-options-parameters"></a>优化查询源选项参数 
可以通过请求的[源选项](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet)参数优化查询性能。 尝试设置以下选项：

  * 首先将 `MaxDegreeOfParallelism` 设置为-1，然后在不同的值之间比较性能。 
  * 首先将 `MaxBufferedItemCount` 设置为-1，然后在不同的值之间比较性能。 
  * 将 `MaxItemCount` 设置为-1。

比较不同值的性能时，请尝试 2、4、8、16 等值。
 
## <a name="read-all-results-from-continuations"></a>读取延续值的所有结果
如果认为自己没有获得所有结果，请确保将延续值清空完毕。 换而言之，只要延续标记会生成更多文档，就继续读取结果。

可以通过以下模式之一实现完全清空：

  * 继续处理结果，而继续不为空。
  * 继续处理，同时查询有更多结果。 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>选择使用索引的系统函数
如果表达式可以转换为一系列字符串值，则它可以使用索引，否则不可使用索引。 

下面是可以使用索引的字符串函数的列表： 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr，但前提是第一个 num_expr 为 0 
    
    下面是几个查询示例： 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * 避免使用索引不提供的筛选器（或 WHERE 子句）中的系统函数。 此类系统函数的一些示例包括 Contains、Upper、Lower。
  * 请尽量将查询编写为使用基于分区键的筛选器。
  * 若要实现高性能查询，请避免在筛选器中调用上部/较低。 而是在插入时规范化值的大小写。 对于每个值，插入具有所需大小写的值，或插入原始值和具有所需大小写的值。 

    例如：
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    对于这种情况，请将 "JOE" 大写或存储 "Joe" 的原始值和 "JOE"。 
    
    如果 JSON 数据大小写规范化，则查询将变为：
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    第二个查询的性能更高，因为它不需要对每个值进行转换以将值与 "JOE" 进行比较。

有关更多系统函数的详细信息，请参阅[系统功能](sql-query-system-functions.md)一文。

## <a name="check-indexing-policy"></a>检查索引策略
若要验证当前[索引策略](index-policy.md)是否已优化，请执行以下操作：

  * 确保查询中使用的所有 JSON 路径都包含在索引策略中，以便更快地进行读取。
  * 排除查询中未使用的路径以获得更高性能的写入。

有关更多详细信息，请参阅[如何管理索引策略](how-to-manage-indexing-policy.md)一文。

## <a name="spatial-data-check-ordering-of-points"></a>空间数据：检查点排序
多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。

## <a name="optimize-join-expressions"></a>优化联接表达式
`JOIN` 表达式可扩展到大型叉积。 如果可能，请通过更窄的筛选器来针对较小的搜索空间进行查询。

多值子查询可以优化 `JOIN` 表达式，方法是：在每个 select-多个表达式后推送谓词，而不是在 `WHERE` 子句中的所有交叉联接后推送。 有关详细示例，请参阅[优化联接表达式](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions)一文。

## <a name="optimize-order-by-expressions"></a>优化 ORDER BY 表达式 
如果字段是稀疏或未包含在索引策略中，则 `ORDER BY` 查询性能可能会降低。

  * 对于诸如时间之类的稀疏字段，可以在筛选器中尽量减少搜索空间。 
  * 对于单个属性 `ORDER BY`，请在索引策略中包括属性。 
  * 对于多个属性 `ORDER BY` 表达式，请对要排序的字段定义[组合索引](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes)。  

## <a name="many-large-documents-being-loaded-and-processed"></a>正在加载和处理的多个大文档
查询所需的时间和每个 ru 不仅依赖于响应的大小，还取决于查询处理管道完成的工作。 时间和 ru 按整个查询处理管道完成的工作量比例增加。 对于大型文档，需要执行更多的工作，因此需要更多的时间和多个 ru 来加载和处理大型文档。

## <a name="low-provisioned-throughput"></a>预配的吞吐量较低
确保预配的吞吐量可以处理工作负荷。 增加受影响的集合的 RU 预算。

## <a name="try-upgrading-to-the-latest-sdk-version"></a>尝试升级到最新的 SDK 版本
若要确定最新的 SDK，请参阅[Sdk 下载和发行说明](sql-api-sdk-dotnet.md)一文。

## <a name="next-steps"></a>后续步骤
请参阅以下文档，了解如何根据查询度量每个查询，获取执行统计信息来优化查询，以及执行其他操作：

* [使用 .NET SDK 获取 SQL 查询执行指标](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 性能提示](performance-tips.md)
