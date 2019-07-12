---
title: 诊断和排查查询问题时使用 Azure Cosmos DB
description: 了解如何识别、 诊断和排查 Azure Cosmos DB SQL 查询问题。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 079e8677febfe6683d4f0e60a0e7ba6b06ea549d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835840"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Azure Cosmos DB 的查询性能故障排除
这篇文章介绍如何识别、 诊断和排查 Azure Cosmos DB SQL 查询问题。 若要获得最佳性能的 Azure Cosmos DB 查询，请执行以下故障排除步骤。 

## <a name="collocate-clients-in-same-azure-region"></a>将同一 Azure 区域中的客户端并置 
通过确保在与预配 Azure Cosmos DB 终结点所在的同一 Azure 区域中调用应用程序，可能会实现最低的延迟。 有关可用区域的列表，请参阅[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/#services)一文。

## <a name="check-consistency-level"></a>检查一致性级别
[一致性级别](consistency-levels.md)可能会影响性能和费用。 请确保您的一致性级别适用于给定的方案。 有关更多详细信息，请参阅[选择一致性级别](consistency-levels-choosing.md)。

## <a name="log-query-metrics"></a>日志查询指标
使用`QueryMetrics`来解决速度缓慢或昂贵的查询。 

  * 设置`FeedOptions.PopulateQueryMetrics = true`具有`QueryMetrics`在响应中。
  * `QueryMetrics` 类具有一个已重载`.ToString()`函数可调用以获取的字符串表示形式的`QueryMetrics`。 
  * 可以使用度量值派生的其他项中的以下见解： 
  
      * 是否在查询管道的任何特定组件花费了异常长时间才能完成 （在数百毫秒或更多的顺序）。 

          * 看看`TotalExecutionTime`。
          * 如果`TotalExecutionTime`查询的是早于端到端执行时间，则客户端或网络中所花费的时间。 仔细检查并置的客户端和 Azure 区域。
      
      * 在文档中是否没有误报分析 （如果输出文档数是要少得多比检索文档计数）。  

          * 看看`Index Utilization`。
          * `Index Utilization` = (返回的文档数 / 加载的文档)
          * 如果返回的文档数远远小于加载数，然后与正在分析的假正值。
          * 限制要检索带有较窄的筛选器的文档数。  

      * 取得了不错的个别往返过程 (请参阅`Partition Execution Timeline`的字符串表示形式从`QueryMetrics`)。 
      * 是否在查询占用高的请求费用。 

有关更多详细信息，请参阅[如何获取 SQL 查询执行指标](profile-sql-api-query.md)一文。
      
## <a name="tune-query-feed-options-parameters"></a>优化查询源的选项参数 
可以通过请求的优化查询性能[馈送选项](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet)参数。 请尝试设置以下选项：

  * 设置`MaxDegreeOfParallelism`为-1，然后在不同的值之间的比较性能。 
  * 设置`MaxBufferedItemCount`为-1，然后在不同的值之间的比较性能。 
  * 设置`MaxItemCount`为-1。

在比较不同的值的性能，请尝试值，例如 2、 4、 8、 16 和其他人。
 
## <a name="read-all-results-from-continuations"></a>从延续中读取所有结果
如果您认为您未获得所有结果，请确保完全清空延续任务。 换而言之，请继续标记有更多文档，以生成读取结果。

完全清空可以实现与以下模式之一：

  * 延续不为空时，请继续处理结果。
  * 继续处理而查询具有更多结果。 

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

## <a name="choose-system-functions-that-utilize-index"></a>选择利用索引的系统函数
如果表达式可以转换为字符串值的范围，然后它可以利用索引;否则，不能。 

下面是可以利用索引的字符串函数的列表： 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, but only if first num_expr is 0 
    
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

  * 避免在筛选器中的系统函数 （或 WHERE 子句） 的不是由索引。 此类系统函数的一些示例包括 Contains、 右上、 较低。
  * 请尽量将查询编写为使用基于分区键的筛选器。
  * 若要实现高性能查询，请避免在筛选器中调用大写/小写。 相反，规范化大小写的插入时的值。 对于每个值插入的值与所需的大小写，或插入的原始值和所需的大小写的值。 

    例如：
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    这种情况下，应用商店"JOE"首字母大写，或存储"Joe"的原始值和"JOE"。 
    
    如果 JSON 数据大小写规范化查询变得：
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    第二个查询将为更高的性能，因为它不需要为了比较这些值与"JOE"对每个值执行转换。

有关更多的系统函数的详细信息请参阅[系统函数](sql-query-system-functions.md)一文。

## <a name="check-indexing-policy"></a>检查索引编制策略
若要验证的当前[索引策略](index-policy.md)最佳选择：

  * 请确保在查询中使用的所有 JSON 路径都包含在索引策略从而可以更快地读取。
  * 排除在查询中未用于多个高性能写入的路径。

有关更多详细信息，请参阅[如何为管理索引策略](how-to-manage-indexing-policy.md)一文。

## <a name="spatial-data-check-ordering-of-points"></a>空间数据：检查点的顺序
多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。

## <a name="optimize-join-expressions"></a>优化 JOIN 表达式
`JOIN` 表达式可以扩展到大型跨产品。 在可能的对较小的搜索查询空间通过更窄的筛选器。

多值子查询可以优化`JOIN`通过将谓词推送后每个选择多表达式而不中的所有交叉联接后的表达式`WHERE`子句。 有关详细的示例，请参阅[优化联接表达式](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions)一文。

## <a name="optimize-order-by-expressions"></a>优化 ORDER BY 表达式 
`ORDER BY` 如果这些字段为稀疏或不包含在索引策略中，查询性能可能会受到影响。

  * 对于如时间的稀疏字段，请使用筛选器减少尽可能多地搜索空间。 
  * 单一属性`ORDER BY`，包括在索引策略中的属性。 
  * 为多个属性`ORDER BY`表达式，定义[组合索引](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes)对字段进行排序。  

## <a name="many-large-documents-being-loaded-and-processed"></a>正在加载和处理的许多大型文档
时间和所需的查询的 Ru 不只依赖于响应的大小，它们也是依赖于可通过查询处理管道的工作。 完成的整个查询处理管道工作的大量时间和 ru 数成比例增加。 为大型文档执行更多的工作，因此更多时间和 Ru 所需加载和处理大型文档。

## <a name="low-provisioned-throughput"></a>低预配的吞吐量
请确保预配的吞吐量可以处理工作负荷。 增加 RU 预算为受影响的集合。

## <a name="try-upgrading-to-the-latest-sdk-version"></a>请尝试升级到最新 SDK 版本
若要确定最新 SDK，请参阅[SDK 下载和发行说明](sql-api-sdk-dotnet.md)一文。

## <a name="next-steps"></a>后续步骤
请参阅以下文档如何测量每个查询的 Ru，获取执行统计信息来优化您的查询，和的详细信息：

* [获取 SQL 查询执行指标使用.NET SDK](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 的性能提示](performance-tips.md)