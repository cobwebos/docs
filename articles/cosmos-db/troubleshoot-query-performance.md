---
title: 排查使用 Azure Cosmos DB 时遇到的查询问题
description: 了解如何识别、诊断和排查 Azure Cosmos DB SQL 查询问题。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/20/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 4a8b61f3719a60af567d10f8839987e613babc9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870457"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>排查使用 Azure Cosmos DB 时遇到的查询问题

本文逐步说明排查 Azure Cosmos DB 中的查询问题的一般建议方法。 尽管您不应将本文中概述的步骤视为针对潜在查询问题的完整防御，但此处包含了最常见的性能提示。 应使用本文作为在 Azure Cosmos DB 核心 （SQL） API 中解决慢速或昂贵查询的起始位置。 您还可以使用[诊断日志](cosmosdb-monitor-resource-logs.md)来标识速度慢或消耗大量吞吐量的查询。

可对 Azure Cosmos DB 中的查询优化进行广泛分类：

- 减少查询请求单元 （RU） 费用的优化
- 仅减少延迟的优化

如果减少查询的 RU 费用，几乎肯定会减少延迟。

本文提供了可以使用[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集重新创建的示例。

## <a name="common-sdk-issues"></a>常见 SDK 问题

- 为了获得最佳性能，请遵循[性能提示](performance-tips.md)。
    > [!NOTE]
    > 为提高性能，我们建议处理 Windows 64 位主机。 SQL SDK 包括一个本机 ServiceInterop.dll，用于在本地解析和优化查询。 服务 Interop.dll 仅在 Windows x64 平台上受支持。 对于 Linux 和其他不支持的平台，其中 ServiceInterop.dll 不可用，将对网关进行额外的网络调用以获取优化的查询。
- 您可以为查询设置`MaxItemCount`a，但不能指定最小项计数。
    - 代码应处理任何页面大小，从零到`MaxItemCount`。
    - 页面中的项数将始终小于指定的`MaxItemCount`。 但是，`MaxItemCount`严格来说，结果可能少于此金额。
- 有时，即使将来页上有结果，查询也可能有空页。 原因可能是：
    - SDK 可以执行多个网络调用。
    - 查询可能需要很长时间才能检索文档。
- 所有查询都有一个延续令牌，允许查询继续。 请确保完全耗尽查询。 查看 SDK 示例，并使用`while`循环`FeedIterator.HasMoreResults`来耗尽整个查询。

## <a name="get-query-metrics"></a>获取查询指标

在 Azure Cosmos DB 中优化查询时，第一步始终是[获取查询的查询指标](profile-sql-api-query.md)。 这些指标也可通过 Azure 门户获得：

[![获取查询指标](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

获取查询指标后，将检索的文档计数与查询的输出文档计数进行比较。 使用此比较可以确定本文中要审阅的相关部分。

“已检索文档计数”是查询需要加载的文档数。 “输出文档计数”是查询结果所需的文档数。 如果检索的文档计数明显高于输出文档计数，则查询中至少有一部分无法使用索引，需要执行扫描。

请参阅以下各节以了解方案的相关查询优化。

### <a name="querys-ru-charge-is-too-high"></a>查询的 RU 开销过高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>检索的文档计数明显高于输出文档计数

- [在索引策略中包括必要的路径。](#include-necessary-paths-in-the-indexing-policy)

- [了解哪些系统函数使用索引。](#understand-which-system-functions-use-the-index)

- [了解使用索引的聚合查询。](#understand-which-aggregate-queries-use-the-index)

- [修改同时具有筛选器和 ORDER BY 子句的查询。](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

- [使用子查询优化 JOIN 表达式。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>“已检索文档计数”约等于“输出文档计数”

- [避免跨分区查询。](#avoid-cross-partition-queries)

- [优化具有多个属性筛选器的查询。](#optimize-queries-that-have-filters-on-multiple-properties)

- [修改同时具有筛选器和 ORDER BY 子句的查询。](#modify-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查询的 RU 开销可接受，但延迟仍然过高

- [提高接近性。](#improve-proximity)

- [增加预配吞吐量。](#increase-provisioned-throughput)

- [增加最大货币。](#increase-maxconcurrency)

- [增加最大缓冲项目计数。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>“已检索文档计数”超过“输出文档计数”的查询

 “已检索文档计数”是查询需要加载的文档数。 “输出文档计数”是查询结果所需的文档数。 如果检索的文档计数明显高于输出文档计数，则查询中至少有一部分无法使用索引，需要执行扫描。

下面是一个未完全由索引提供的扫描查询示例：

查询：

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

查询指标：

```
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

检索的文档计数 （60，951） 明显高于输出文档计数 （7），因此此查询需要执行扫描。 在这种情况下，系统函数[UPPER（）](sql-query-upper.md)不使用索引。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>在索引策略中包含所需的路径

索引策略应涵盖子句、`WHERE``ORDER BY``JOIN`子句和大多数系统函数中包含的任何属性。 索引策略中指定的路径应与 JSON 文档中的属性相匹配（区分大小写）。

如果在[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集上运行简单查询，则当子句中的属性编制索引时，`WHERE`观察到 RU 费用要低得多：

#### <a name="original"></a>原始

查询：

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
```

索引策略：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 充电：** 409.51 RU

#### <a name="optimized"></a>已优化

更新的索引策略：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU 充电：** 2.98 RU

您可以随时向索引策略添加属性，而不会影响写入可用性或性能。 如果向索引添加新属性，则使用 该属性的查询将立即使用新的可用索引。 查询将在生成新索引时使用。 因此，在索引重建进行中，查询结果可能不一致。 如果对新属性进行索引，则在索引重建期间，仅使用现有索引的查询不会受到影响。 可以[跟踪索引转换进度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

### <a name="understand-which-system-functions-use-the-index"></a>了解哪些系统函数使用索引

如果表达式可以转换为字符串值范围，则可以使用索引。 否则，它不能。

下面是一些可以使用索引的常见字符串函数的列表：

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING（str_expr、num_expr、num_expr） = str_expr，但前提是第一个num_expr为 0

以下是一些不使用索引且必须加载每个文档的常见系统函数：

| **系统功能**                     | **优化思路**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 使用 Azure 搜索进行全文搜索。                        |
| UPPER/LOWER                             | 而不是使用系统函数来规范化数据进行比较，而是在插入时规范化套管。 类似```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```的查询```SELECT * FROM c WHERE c.name = 'BOB'```变为 。 |
| 数学函数（非聚合） | 如果需要在查询中频繁计算值，请考虑将该值存储为 JSON 文档中的属性。 |

------

查询的其他部分可能仍使用索引，即使系统功能不。

### <a name="understand-which-aggregate-queries-use-the-index"></a>了解使用索引的聚合查询

在大多数情况下，Azure Cosmos DB 中的聚合系统函数将使用索引。 但是，根据聚合查询中的筛选器或其他子句，可能需要查询引擎来加载大量文档。 通常，查询引擎将首先应用相等和范围筛选器。 应用这些筛选器后，查询引擎可以评估其他筛选器，并根据需要加载剩余文档以计算聚合。

例如，给定这两个示例查询，具有相等性和`CONTAINS`系统函数筛选器的查询通常比仅具有系统函数筛选器的`CONTAINS`查询更有效。 这是因为首先应用相等性筛选器，并在需要加载文档之前使用索引来为更昂贵的`CONTAINS`筛选器加载。

仅`CONTAINS`具有筛选器的查询 - 较高的 RU 费用：

```sql
SELECT COUNT(1) FROM c WHERE CONTAINS(c.description, "spinach")
```

同时使用相等筛选器和`CONTAINS`筛选器的查询 - 较低的 RU 电荷：

```sql
SELECT AVG(c._ts) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

下面是不会完全使用索引的聚合查询的其他示例：

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>具有不使用索引的系统函数的查询

您应该参考相关[系统函数的页面](sql-query-system-functions.md)，看看它是否使用索引。

```sql
SELECT MAX(c._ts) FROM c WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>使用用户定义的函数聚合查询（UDF）

```sql
SELECT AVG(c._ts) FROM c WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>与组 BY 的查询

RU 的`GROUP BY`电荷将随着`GROUP BY`子句中属性基数的增加而增加。 在此示例中，查询引擎必须加载与`c.foodGroup = "Sausages and Luncheon Meats"`筛选器匹配的每个文档，以便 RU 电荷预期很高。

```sql
SELECT COUNT(1) FROM c WHERE c.foodGroup = "Sausages and Luncheon Meats" GROUP BY c.description
```

如果计划经常运行相同的聚合查询，则使用[Azure Cosmos DB 更改源](change-feed.md)构建实时具体视图可能比运行单个查询更有效。

### <a name="modify-queries-that-have-both-a-filter-and-an-order-by-clause"></a>修改同时具有筛选器和 ORDER BY 子句的查询

尽管具有筛选器和`ORDER BY`子句的查询通常使用范围索引，但如果可以从复合索引中提供它们，则它们的效率会更高。 除了修改索引策略以外，还应将组合索引中的所有属性添加到 `ORDER BY` 子句。 对查询的此更改将确保它使用复合索引。  您可以通过对[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集运行查询来观察影响：

#### <a name="original"></a>原始

查询：

```sql
SELECT * FROM c WHERE c.foodGroup = "Soups, Sauces, and Gravies" ORDER BY c._ts ASC
```

索引策略：

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 充电：** 44.28 RU

#### <a name="optimized"></a>已优化

更新的查询（包含 `ORDER BY` 子句中的两个属性）：

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

更新的索引策略：

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 充电：** 8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查询优化 JOIN 表达式
多值子查询可以通过在每个选择多`JOIN`表达式之后推送谓词来优化表达式，而不是在`WHERE`子句中的所有交叉联接之后。

请看下面的查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 充电：** 167.62 RU

对于此查询，索引将匹配包含名为“infant formula”的标记、nutritionValue 大于 0 且份量大于 1 的任何文档。 此处的 `JOIN` 表达式将针对应用任何筛选器之前的每个匹配文档，执行所有 tags、nutrients 和 servings 数组项的叉积计算。 然后，`WHERE` 子句将针对每个 `<c, t, n, s>` 元组应用筛选谓词。

例如，如果匹配的文档在三个数组中每个数组中有 10 个项目，它将扩展到 1 x 10 x 10 x 10（即 1，000 个）tup。 此处使用子查询有助于在加入下一个表达式之前筛选出联接的数组项。

此查询等效于前面的查询，但使用了子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 充电：** 22.17 RU

假设标记数组中只有一个项目与筛选器匹配，并且营养和服务数组有五个项目。 表达式`JOIN`将扩展到 1 x 1 x 5 x 5 = 25 项，而第一个查询中的 1，000 项。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>“已检索文档计数”等于“输出文档计数”的查询

如果检索的文档计数大致等于输出文档计数，则查询不必扫描许多不必要的文档。 对于许多查询（如使用 TOP 关键字的查询），检索的文档计数可能超过输出文档计数 1。 你不需要担心这个。

### <a name="avoid-cross-partition-queries"></a>避免跨分区查询

当请求单位和数据存储需求提高时，Azure Cosmos DB 将使用[分区](partitioning-overview.md)来扩展单个容器。 每个物理分区具有不同的独立索引。 如果查询具有与容器分区键匹配的相等筛选器，则只需检查相关分区的索引。 此优化减少了查询所需的总 R。

如果预配的 RU 数量很大（超过 30，000 个）或存储了大量数据（超过 100 GB），则可能有足够的容器，可以显著减少查询 RU 费用。

例如，如果创建具有分区密钥 foodGroup 的容器，则以下查询只需检查单个物理分区：

```sql
SELECT * FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

这些查询还将通过在查询中添加分区键进行优化：

```sql
SELECT * FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

分区键上具有范围筛选器的查询，或者分区键上没有任何筛选器的查询，将需要检查每个物理分区的索引的结果：

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>优化具有多个属性筛选器的查询

尽管具有多个属性筛选器的查询通常使用范围索引，但如果可以从复合索引中提供筛选器，则它们的效率会更高。 对于少量数据，此优化不会产生重大影响。 然而，对于大量数据来说，它可能是有用的。 对于每个组合索引，最多只能优化一个非相等性筛选器。 如果查询有多个非相等筛选器，请选择其中一个将使用复合索引的筛选器。 其余将继续使用范围索引。 非相等性筛选器必须在组合索引中最后定义。 [了解有关复合索引的更多](index-policy.md#composite-indexes)。

下面是一些可以使用复合索引进行优化的查询示例：

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

下面是相关的复合索引：

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>减少查询延迟的优化

在许多情况下，当查询延迟仍然过高时，RU 费用可能是可以接受的。 以下各节概述了减少查询延迟的提示。 如果对同一个数据集多次运行同一个查询，该查询每次都会产生相同的 RU 开销。 但是查询延迟可能因查询执行而异。

### <a name="improve-proximity"></a>改善邻近性

从与 Azure Cosmos DB 帐户不同的区域运行的查询的延迟将高于在同一区域内运行的查询。 例如，如果在台式计算机上运行代码，则预计延迟比查询来自与 Azure Cosmos DB 位于同一 Azure 区域中的虚拟机的延迟高出数十毫秒或数百毫秒。 在[Azure Cosmos DB 中全局分发数据](distribute-data-globally.md)非常简单，以确保数据更接近应用。

### <a name="increase-provisioned-throughput"></a>增大预配吞吐量

在 Azure Cosmos DB 中，预配吞吐量以请求单位 （R） 为单位进行测量。 假设您的查询消耗了 5 个 R 的吞吐量。 例如，如果预配 1，000 个 R，则可以每秒运行该查询 200 次。 如果尝试在没有足够的可用吞吐量时运行查询，Azure Cosmos DB 将返回 HTTP 429 错误。 任何当前核心 （SQL） API SDK 将在短暂等待后自动重试此查询。 限制请求需要更长的时间，因此增加预配吞吐量可以提高查询延迟。 您可以在 Azure 门户的 **"指标"** 边栏选项卡上观察[受限制的请求总数](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

### <a name="increase-maxconcurrency"></a>增大 MaxConcurrency

并行查询的工作原理是并行查询多个分区。 但是，单个分区集合中的数据相对于查询进行串行提取。 因此，如果将 MaxConcurrency 设置为分区数，则只要所有其他系统条件保持不变，则最有可能实现性能最优的查询。 如果您不知道分区的数量，则可以将 MaxConcurrency（或旧 SDK 版本中的 MaxAtoinsParallelism）设置为高数字。 系统将选择最小（分区数，用户提供的输入）作为最大并行度。

### <a name="increase-maxbuffereditemcount"></a>增大 MaxBufferedItemCount

查询设计为当客户端正在处理当前结果批时预先提取结果。 预提取有助于提高查询的总体延迟。 设置 MaxBufferedItemCount 会限制预获取结果的数量。 如果将此值设置为返回的预期结果数（或较高的数字），则查询可以从预提取中获得最大好处。 如果将此值设置为 -1，系统将自动确定要缓冲的项数。

## <a name="next-steps"></a>后续步骤
有关如何测量每个查询的 R，获取用于调整查询的执行统计信息的信息，请参阅以下文章，以及详细信息：

* [使用 .NET SDK 获取 SQL 查询执行指标](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 性能提示](performance-tips.md)
