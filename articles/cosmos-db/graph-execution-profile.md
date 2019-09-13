---
title: 使用 Azure Cosmos DB Gremlin API 的执行配置文件函数评估查询
description: 了解如何使用执行配置文件步骤改善 Gremlin 查询及排查其问题。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: ab5c55105eeb912281f35e3d6094c0c43a76f89a
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915884"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>如何使用执行配置文件步骤来评估 Gremlin 查询

本文概述如何使用适用于 Azure Cosmos DB Gremlin API 图形数据库的执行配置文件步骤。 此步骤提供故障排除和查询优化的相关信息，适用于可以针对 Cosmos DB Gremlin API 帐户执行的任何 Gremlin 查询。

若要使用此步骤，只需在 Gremlin 查询的末尾追加 `executionProfile()` 函数调用即可。 **将执行你的 Gremlin 查询**，操作结果将返回包含查询执行配置文件的 JSON 响应对象。

例如：

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

调用 `executionProfile()` 步骤后，响应将是一个 JSON 对象，其中包含执行的 Gremlin 步骤、该步骤花费的总时间，以及语句生成的 Cosmos DB 运行时运算符数组。

> [!NOTE]
> Apache Tinkerpop 规范中未定义此执行配置文件实现。 它是特定于 Azure Cosmos DB Gremlin API 的实现。


## <a name="response-example"></a>响应示例

下面是将会返回的带批注的输出示例：

> [!NOTE]
> 此示例使用注释做了批注，以便解释响应的常规结构。 实际的 executionProfile 响应不包含任何注释。

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> executionProfile 步骤将执行 Gremlin 查询。 此查询包含 `addV` 或 `addE` 步骤，这些步骤会完成创建过程，并提交查询中指定的更改。 因此，Gremlin 查询生成的请求单位也会产生费用。

## <a name="execution-profile-response-objects"></a>执行配置文件响应对象

executionProfile() 函数的响应将生成采用以下结构的 JSON 对象层次结构：
  - **Gremlin 操作对象**：表示已执行的整个 Gremlin 操作。 包含以下属性。
    - `gremlin`：已执行的显式 Gremlin 语句。
    - `totalTime`：执行该步骤所花费的时间（以毫秒为单位）。 
    - `metrics`：一个数组，其中包含为了完成查询而执行的每个 Cosmos DB 运行时运算符。 此列表已按执行顺序排序。
    
  - **Cosmos DB 运行时运算符**：表示整个 Gremlin 操作的每个组件。 此列表已按执行顺序排序。 每个对象包含以下属性：
    - `name`：运算符的名称。 这是已评估和执行的步骤的类型。 请在下表中了解详细信息。
    - `time`：给定的运算符所花费的时间（以毫秒为单位）。
    - `annotations`：包含特定于已执行的运算符的其他信息。
    - `annotations.percentTime`：执行特定运算符所花费的时间占总时间的百分比。
    - `counts`：此运算符从存储层返回的对象数。 此值包含在内部的 `counts.resultCount` 标量值中。
    - `storeOps`：表示可以跨一个或多个分区的存储操作。
    - `storeOps.fanoutFactor`：表示此特定存储操作访问的分区数。
    - `storeOps.count`：表示此存储操作返回的结果数。
    - `storeOps.size`：表示给定存储操作的结果大小（以字节为单位）。

Cosmos DB Gremlin 运行时运算符|描述
---|---
`GetVertices`| 此步骤从持久性层获取一组带谓词的对象。 
`GetEdges`| 此步骤获取与一组顶点相邻的边缘。 此步骤可以生成一个或多个存储操作。
`GetNeighborVertices`| 此步骤获取与一组边缘相连接的顶点。 边缘包含分区键及其源和目标顶点的 ID。
`Coalesce`| 每当执行 `coalesce()` Gremlin 步骤时，此步骤都会考虑两项操作的评估结果。
`CartesianProductOperator`| 此步骤计算两个数据集之间的笛卡儿积。 通常，每当使用谓词 `to()` 或 `from()`，都会执行此步骤。
`ConstantSourceOperator`| 此步骤计算一个表达式，以生成一个常量值作为结果。
`ProjectOperator`| 此步骤使用先前操作的结果来准备并序列化响应。
`ProjectAggregation`| 此步骤准备并序列化聚合操作的响应。

> [!NOTE]
> 随着新运算符的添加，此列表将不断更新。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>有关如何分析执行配置文件响应的示例

下面是可以使用执行配置文件响应发现的常用优化方法示例：
  - 盲目扇出查询。
  - 未筛选的查询。

### <a name="blind-fan-out-query-patterns"></a>盲目扇出查询模式

假设某个**分区图形**返回了以下执行配置文件响应：

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

可以从中得出以下结论：
- 该查询是单个 ID 查找，因为 Gremlin 语句遵循 `g.V('id')` 模式。
- 从 `time` 指标判断，此查询的延迟似乎很高，因为它[针对某个单点读取操作花费了 10 毫秒以上](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)。
- 查看 `storeOps` 对象可以发现 `fanoutFactor` 为 `5`，这意味着，此操作访问了 [5 个分区](https://docs.microsoft.com/azure/cosmos-db/partition-data)。

根据此分析的结论，我们可以确定，第一个查询不必要地访问了多余的分区。 在查询中指定分区键作为谓词可以解决此问题。 这样可以降低延迟以及每个查询的开销。 详细了解[图形分区](graph-partitioning.md)。 更佳的查询是 `g.V('tt0093640').has('partitionKey', 't1001')`。

### <a name="unfiltered-query-patterns"></a>未筛选的查询模式

比较以下两个执行配置文件响应。 为简单起见，这些示例使用了单个分区图形。

这第一个查询检索所有带有 `tweet` 标签的顶点，然后获取其相邻顶点：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

在浏览相邻顶点之前，请注意同一查询（但现在它具有附加的筛选器 `has('lang', 'en')`）的配置文件：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

这两个查询达到了相同的效果，但是，第一个查询需要更多的请求单位，因为它在查询相邻项之前，需要迭代一个更大的初始数据集。 比较两个响应中的以下参数时，可以看到此行为的指示器：
- 第一个响应中的 `metrics[0].time` 值更大，表示解决此步骤所花费的时间更长。
- 第一个响应中的 `metrics[0].counts.resultsCount` 值也更大，表示初始工作数据集更大。

## <a name="next-steps"></a>后续步骤
* 了解 Azure Cosmos DB [支持的 Gremlin 功能](gremlin-support.md)。 
* 详细了解 [Azure Cosmos DB 中的 Gremlin API](graph-introduction.md)。
