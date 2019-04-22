---
title: Azure Cosmos DB Gremlin api 评估您的查询执行分析函数
description: 了解如何排除故障并提高 Gremlin 查询使用单步执行配置文件。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288601"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>如何使用执行配置文件步骤来评估 Gremlin 查询

本文概述如何使用 Azure Cosmos DB Gremlin API 图形数据库的单步执行配置文件。 此步骤提供有关疑难解答的相关信息和查询优化，它是可以针对 Cosmos DB Gremlin API 帐户执行任何 Gremlin 查询与兼容。

若要使用此步骤中，只需附加`executionProfile()`函数 Gremlin 查询结束时调用。 **将执行 Gremlin 查询**和操作的结果将返回与查询执行配置文件的 JSON 响应对象。

例如：

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

在调用`executionProfile()`步骤中，响应将是一个 JSON 对象，包括执行的 Gremlin 步骤、 花费的总时间以及该语句导致的 Cosmos DB 运行时运算符的数组。

> [!NOTE]
> 在 Apache Tinkerpop 规范中未定义执行配置文件的此实现。 它是特定于 Azure Cosmos DB Gremlin API 实现。


## <a name="response-example"></a>响应示例

以下是输出的带批注的将返回示例：

> [!NOTE]
> 此示例将其中的注释解释响应的一般结构批注。 实际 executionProfile 响应不包含任何注释。

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
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
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
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
> ExecutionProfile 步骤将执行 Gremlin 查询。 这包括`addV`或`addE`步骤，这将导致创建并将提交所做的更改在查询中指定。 因此，通过 Gremlin 查询生成的请求单位也将进行收费。

## <a name="execution-profile-response-objects"></a>执行配置文件的响应对象

ExecutionProfile() 函数的响应将生成具有以下结构的 JSON 对象的层次结构：
  - **Gremlin 操作对象**:表示整个已执行的 Gremlin 操作。 包含以下属性。
    - `gremlin`：已执行了 explicit Gremlin 语句。
    - `totalTime`：时间 （毫秒），执行步骤中产生的。 
    - `metrics`：一个数组，包含每个已执行以满足查询的 Cosmos DB 运行时运算符。 此列表被按执行顺序。
    
  - **Cosmos DB 运行时运算符**:表示每个组件的整个 Gremlin 操作。 此列表被按执行顺序。 每个对象包含以下属性：
    - `name`：操作员名称。 这是步骤的已计算并在执行的类型。 下表中了解的详细信息。
    - `time`：时间 （毫秒），一个给定的运算符所执行的量。
    - `annotations`：包含特定于已执行的运算符的详细信息。
    - `annotations.percentTime`：执行特定的运算符所用的总时间的百分比。
    - `counts`：从存储层此运算符返回的对象数。 这包含在`counts.resultCount`中的标量值。
    - `storeOps`：表示可以跨一个或多个分区的存储操作。
    - `storeOps.fanoutFactor`：表示此特定的存储操作访问的分区数。
    - `storeOps.count`：表示此存储操作返回的结果数。
    - `storeOps.size`：表示以字节为单位的给定的存储操作的结果的大小。

Cosmos DB Gremlin 运行时运算符|描述
---|---
`GetVertices`| 此步骤从持久性层获取一组谓词的对象。 
`GetEdges`| 此步骤中获取与一组顶点的相邻的边缘。 此步骤可能会导致一个或多个存储操作。
`GetNeighborVertices`| 此步骤中获取连接到边缘的一组顶点。 边缘包含分区键和 id。 其源和目标顶点。
`Coalesce`| 此步骤中的两个操作的评估帐户每当`coalesce()`执行 Gremlin 步骤。
`CartesianProductOperator`| 此步骤中计算两个数据集之间笛卡儿积。 通常只要执行谓词`to()`或`from()`使用。
`ConstantSourceOperator`| 此步骤中计算表达式结果生成的常量值。
`ProjectOperator`| 此步骤中准备和序列化使用上述操作的结果的响应。
`ProjectAggregation`| 此步骤中准备和序列化聚合操作的响应。

> [!NOTE]
> 此列表将继续更新，因为添加新的运算符。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>有关如何分析执行配置文件响应示例

可以使用执行分析响应发现的常用优化方法的示例如下：
  - 密件扇出查询。
  - 未筛选的查询。

### <a name="blind-fan-out-query-patterns"></a>密件扇出查询模式

假设从以下的执行配置文件响应**分区的图形**:

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

可以从它进行以下结论：
- 查询是单个 ID 查找，因为 Gremlin 语句遵循模式`g.V('id')`。
- 从判断`time`度量值，此查询的延迟时间似乎很高，因为它是[多个单个点读取操作的 10 毫秒](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)。
- 如果我们进行调查`storeOps`对象，我们可以看到`fanoutFactor`是`5`，这意味着[5 个分区](https://docs.microsoft.com/azure/cosmos-db/partition-data)访问了此操作。

为此分析的结果，我们可以确定第一个查询访问不必要的更多分区。 通过在用作谓词的查询中指定分区键可以解决此问题。 这将导致更少的延迟和更低的每个查询的成本。 详细了解如何[graph 分区](graph-partitioning.md)。 更优的查询将`g.V('tt0093640').has('partitionKey', 't1001')`。

### <a name="unfiltered-query-patterns"></a>未筛选的查询模式

比较以下两个执行配置文件响应。 为简单起见，这些示例使用单个分区的图形。

此第一个查询检索所有顶点的标签`tweet`，然后获取其相邻顶点：

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

请注意，配置文件相同的查询，但现在具有的一个附加的筛选器，`has('lang', 'en')`之前浏览相邻顶点,：

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

这两个查询达到相同的结果，但是，第一个将需要更多请求单位，因为它需要循环更大的初始数据集访问查询相邻项之前。 比较这两个响应中的以下参数时，我们可以看到此行为的指示器：
- `metrics[0].time`值是在第一个响应，指示此单步执行所花更长时间才能解决更高版本。
- `metrics[0].counts.resultsCount`值是在第一个响应，这表明初始工作数据集是一个更大更高版本。

## <a name="next-steps"></a>后续步骤
* 了解如何[支持的 Gremlin 功能](gremlin-support.md)Azure Cosmos DB 中。 
* 详细了解如何[在 Azure Cosmos DB 的 Gremlin API](graph-introduction.md)。
