---
title: 诊断和排查使用 Azure Cosmos DB 时遇到的查询问题
description: 了解如何识别、诊断和排查 Azure Cosmos DB SQL 查询问题。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515814"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>排查 Azure Cosmos DB 的查询性能问题
本文介绍如何识别、诊断和排查 Azure Cosmos DB SQL 查询问题。 若要实现 Azure Cosmos DB 查询的最佳性能，请执行以下故障排除步骤。 

## <a name="collocate-clients-in-same-azure-region"></a>将客户端并置在同一 Azure 区域 
通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/#services)一文。

## <a name="check-consistency-level"></a>检查一致性级别
[一致性级别](consistency-levels.md)可能会影响性能和费用。 请确保一致性级别适合给定的方案。 有关更多详细信息，请参阅[选择一致性级别](consistency-levels-choosing.md)。

## <a name="log-the-executed-sql-query"></a>记录已执行的 SQL 查询 

可以在存储帐户或诊断日志表中记录已执行的 SQL 查询。 [利用诊断日志中的 SQL 查询日志](logging.md#turn-on-logging-in-the-azure-portal), 可以在所选的存储帐户中记录模糊查询。 这使你可以查看使用更多 ru 的日志和查找查询。 稍后, 您可以使用活动 id 来匹配 QueryRuntimeStatistics 中的实际查询。 对于安全用途和查询参数名称, 将对查询进行模糊处理, 其中, where 子句中的值与实际名称和值不同。 你可以使用日志记录到存储帐户来保留所执行查询的长期保留。  

## <a name="log-query-metrics"></a>日志查询指标

使用 `QueryMetrics` 排查速度缓慢或开销较高的查询。 

  * 设置 `FeedOptions.PopulateQueryMetrics = true` 以在响应中包含 `QueryMetrics`。
  * `QueryMetrics` 类包含一个重载的 `.ToString()` 函数，调用该函数可以获取 `QueryMetrics` 的字符串表示形式。 
  * 可以利用这些指标来派生以下和其他见解： 
  
      * 完成查询管道的任意特定组件是否异常地花费了很长时间（数百毫秒或更长时间）。 

          * 查看 `TotalExecutionTime`。
          * 如果查询的 `TotalExecutionTime` 小于从头到尾完成执行所花费的时间，则表示时间花费在客户端或网络上。 反复检查客户端和 Azure 区域是否并置在一起。
      
      * 分析的文档中是否存在误报（如果“输出文档计数”比“检索到的文档计数”小得多）。  

          * 查看 `Index Utilization`。
          * `Index Utilization` = (返回的文档数/加载的文档数)
          * 如果返回的文档数比加载的文档数小得多，则表示正在分析误报。
          * 使用范围更窄的筛选器限制检索的文档数。  

      * 各个往返行程的开销如何（查看 `QueryMetrics` 字符串表示形式中的 `Partition Execution Timeline`）。 
      * 查询是否消耗了较多的请求单位。 

有关更多详细信息，请参阅[如何获取 SQL 查询执行指标](profile-sql-api-query.md)一文。
      
## <a name="tune-query-feed-options-parameters"></a>优化查询源选项参数 
可以通过请求的[源选项](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet)参数优化查询性能。 尝试设置以下选项：

  * 先将 `MaxDegreeOfParallelism` 设置为 -1，然后比较不同值的性能。 
  * 先将 `MaxBufferedItemCount` 设置为 -1，然后比较不同值的性能。 
  * 将 `MaxItemCount` 设置为 -1。

比较不同值的性能时，请尝试 2、4、8、16 等值。
 
## <a name="read-all-results-from-continuations"></a>读取延续值的所有结果
如果认为自己没有获得所有结果，请确保将延续值清空完毕。 换而言之，只要延续标记会生成更多文档，就继续读取结果。

可以通过以下模式之一实现完全清空：

  * 在延续值不为空的情况下继续处理结果。
  * 只要查询还有结果，就继续处理。 

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

  * 避免在筛选器（或 WHERE 子句）中使用索引不为其提供服务的系统函数。 此类系统函数的示例包括 Contains、Upper、Lower。
  * 请尽量将查询编写为使用基于分区键的筛选器。
  * 若要实现高性能查询，请避免在筛选器中调用 UPPER/LOWER。 插入时应该规范化值的大小写。 对于每个值，请使用所需的大小写插入该值，或者同时插入原始值以及使用所需大小写的值。 

    例如：
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    在本例中，请存储全大写的“JOE”，或者同时存储“Joe”（原始值）和“JOE”。 
    
    如果已规范化 JSON 数据大小写，则查询将变为：
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    第二个查询的性能更高，因为在将值与“JOE”进行比较时，它不需要对每个值执行转换。

如需更多系统函数详细信息，请参阅[系统函数](sql-query-system-functions.md)一文。

## <a name="check-indexing-policy"></a>检查索引策略
若要验证当前[索引策略](index-policy.md)是否已优化，请执行以下操作：

  * 确保将查询中使用的所有 JSON 路径包括在索引策略中，以加快读取速度。
  * 排除不在查询中使用的路径，以提高写入性能。

有关更多详细信息，请参阅[如何管理索引策略](how-to-manage-indexing-policy.md)一文。

## <a name="spatial-data-check-ordering-of-points"></a>空间数据：检查点顺序
多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。

## <a name="optimize-join-expressions"></a>优化 JOIN 表达式
`JOIN` 表达式可扩展成大型叉积。 如果可能，请通过范围更窄的筛选器针对较小的搜索空间进行查询。

多值子查询可以通过在 `WHERE` 子句中的每个 select-many 表达式后面（而不是所有 cross-join 后面）推送谓词，来优化 `JOIN` 表达式。 有关详细示例，请参阅[优化 Join 表达式](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions)一文。

## <a name="optimize-order-by-expressions"></a>优化 ORDER BY 表达式 
如果字段是稀疏的或未包含在索引策略中，则 `ORDER BY` 查询性能可能会降低。

  * 对于时间之类的稀疏字段，请使用筛选器尽量减小搜索空间。 
  * 对于单属性 `ORDER BY`，请在索引策略中包含属性。 
  * 对于多属性 `ORDER BY` 表达式，请在排序字段中定义[组合索引](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes)。  

## <a name="many-large-documents-being-loaded-and-processed"></a>正在加载和处理许多大型文档
查询所需的时间和 RU 不仅取决于响应大小，而且还取决于查询处理管道完成的工作。 时间和 RU 根据整个查询处理管道完成的工作量成比例增加。 对于大型文档需要执行更多的工作，因此加载和处理大型文档所需的时间和 RU 更多。

## <a name="low-provisioned-throughput"></a>预配吞吐量较低
确保预配的吞吐量可以处理工作负荷。 提高受影响集合的 RU 预算。

## <a name="try-upgrading-to-the-latest-sdk-version"></a>尝试升级到最新 SDK 版本
若要确定最新 SDK 版本是什么，请参阅 [SDK 下载和发行说明](sql-api-sdk-dotnet.md)一文。

## <a name="next-steps"></a>后续步骤
参阅以下文档了解如何度量每个查询的 RU、获取用于优化查询的执行统计信息，等等：

* [使用 .NET SDK 获取 SQL 查询执行指标](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 性能提示](performance-tips.md)
