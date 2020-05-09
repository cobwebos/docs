---
title: 有关 Gremlin API 的常见问题 Azure Cosmos DB
description: 获取有关 Gremlin API 的常见问题的解答 Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 416cf4f027b6f1e72641324be39ba0304301db37
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614494"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>有关 Gremlin API 的常见问题 Azure Cosmos DB

本文介绍 Azure Cosmos DB 中有关 Gremlin API 的一些常见问题的解答。

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>如何评估 Gremlin 查询的效率

**ExecutionProfile()** 预览步骤可用于提供查询执行计划的分析。 此步骤需要添加到任何 Gremlin 查询的末尾，如以下示例所示：

**查询示例**

```
g.V('mary').out('knows').executionProfile()
```

**示例输出**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

上述配置文件的输出显示了获取顶点对象、边对象所花费的时间量，以及工作数据集的大小。 这与 Azure Cosmos DB 查询的标准成本度量相关。

## <a name="other-frequently-asked-questions"></a>其他常见问题解答

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>在图形数据库上运行查询时如何针对每秒的 RU 数目收费？

所有图形对象、顶点和边，都显示为后端中的 JSON 文档。 由于一个 Gremlin 查询一次可以修改一个或许多图形对象，因此与之相关联的开销与该查询处理的对象、边直接相关。 这是 Azure Cosmos DB 对所有其他 API 使用的同一进程。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。

RU 费用取决于遍历的工作数据集，而不是结果集。 例如，如果查询旨在获取单个顶点作为结果，但在此过程中需要遍历多个其他对象，那么成本将取决于计算一个结果顶点所需的全部图形对象。

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>图形数据库可在 Azure Cosmos DB Gremlin API 中拥有的最大规模是怎样的？

Azure Cosmos DB 利用[水平分区](partition-data.md)自动满足增加存储和吞吐量的需求。 工作负荷的最大吞吐量和存储容量由与给定容器关联的分区数量决定。 但是，Gremlin API 容器有一组用于按规模确保适当性能体验的特定准则。 有关分区和最佳做法的详细信息，请参阅 [Azure Cosmos DB 中的分区](partition-data.md)一文。

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>对于 C#/.NET 开发，我是否应使用 Microsoft.Azure.Graphs 包或 Gremlin.NET？

Azure Cosmos DB Gremlin API 利用开源驱动程序作为服务的主要连接器。 因此，建议的选项是使用 [Apache Tinkerpop 支持的驱动程序](https://tinkerpop.apache.org/)。

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>如何使用 Gremlin 驱动程序防范注入式攻击？

大多数本机 Apache Tinkerpop Gremlin 驱动程序允许用户选择为执行查询提供参数字典。 这里提供了有关如何执行此操作的示例（分别以 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) 和 [Gremlin Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js) 编写）。

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>为什么我收到“Gremlin 查询编译错误:找不到任何方法”错误？

Azure Cosmos DB Gremlin API 实现了 Gremlin 图面区域中定义的功能的子集。 有关受支持的步骤和详细信息，请参阅 [Gremlin 支持](gremlin-support.md)一文。

最佳解决方法是使用受支持的功能重新编写所需的 Gremlin 步骤，因为 Azure Cosmos DB 支持所有基本 Gremlin 步骤。

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>为什么我收到“WebSocketException:当状态代码应为‘101’时，服务器返回了状态代码‘200’”错误？

正在使用不正确的终结点时，可能会引发此错误。 导致出现此错误的终结点采用以下模式：

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

这是图形数据库的文档终结点。  要使用的正确终结点是 Gremlin 终结点，它采用以下格式：

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>为什么会收到“RequestRateIsTooLarge”错误？

此错误表示，每秒分配的请求单位不足，无法为查询提供服务。 此错误通常在运行获取所有顶点的查询时出现：

```
// Query example:
g.V()
```

此查询将尝试检索图形中的所有顶点。 因此，此查询的成本将至少等于以 RU 表示的顶点数。 应调整每秒 RU 数目的设置以满足此查询的需要。

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>为何我的 Gremlin 驱动程序连接最终被删除了？

Gremlin 连接是通过 WebSocket 连接进行的。 尽管 WebSocket 连接没有特定生存时间，但 Azure Cosmos DB Gremlin API 将在空闲连接处于非活动状态 30 分钟后对其进行终止。

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>为什么在本机 Gremlin 驱动程序中不能使用 Fluent API 调用？

Fluent API 调用尚不受 Azure Cosmos DB Gremlin API 支持。 Fluent API 调用需要一种称为字节码支持的内部格式设置功能，Azure Cosmos DB Gremlin API 目前不支持此功能。 由于同一原因，最新的 Gremlin-JavaScript 驱动程序当前也不受支持。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB Gremlin 线路协议支持](gremlin-support.md)
* 使用[Gremlin 控制台](create-graph-gremlin-console.md)创建、查询和遍历 Azure Cosmos DB 图形数据库
