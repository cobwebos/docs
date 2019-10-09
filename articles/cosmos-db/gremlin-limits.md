---
title: Azure Cosmos DB Gremlin 的限制
description: 有关 Graph 引擎运行时限制的参考文档
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029846"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 限制
本文介绍 Azure Cosmos DB Gremlin 引擎的限制，并说明他们可能会如何影响客户遍历。

Cosmos DB Gremlin 是在 Cosmos DB 基础结构之上构建的。 因此， [Azure Cosmos DB 服务限制](https://docs.microsoft.com/azure/cosmos-db/concepts-limits)中所述的所有限制仍适用。 

## <a name="limits"></a>限制

达到 Gremlin 限制时，将取消遍历，并使用**x 毫秒状态代码**429 指示阻止错误。 有关详细信息，请参阅[Gremlin server response 标头](gremlin-limits.md)。

**资源**    | **默认限制** | **解释**
--- | --- | ---
脚本长度 | **64 KB** | 每个请求的 Gremlin 遍历脚本的最大长度。
运算符深度 | **400** |  遍历中的唯一步骤的总数。 例如，```g.V().out()``` 的运算符计数为2：V() 和 out()，```g.V('label').repeat(out()).times(100)``` 的运算符深度为 3：V()、repeat() 和 out()，因为 ```.times(100)``` 为 ```.repeat()``` 运算符的参数。
*并行度* | **32** | 发往存储层的单个请求中查询的存储分区的最大数。 具有数百个分区的图将受到此限制的影响。
重复限制 | **32** | ```.repeat()``` 运算符可执行的最大迭代次数。 在大多数情况下，```.repeat()``` 步骤的每次迭代都会运行广度优先的遍历，这意味着任何遍历都限于顶点之间最多 32 个跃点。
遍历超时 | **30 秒** | 超过此时间时，遍历将取消。 Cosmos DB Graph 是一种 OLTP 数据库，其中绝大部分遍历都在几毫秒内完成。 若要对 Cosmos DB Graph 运行 OLAP 查询，请使用 [Apache Spark](https://azure.microsoft.com/services/cosmos-db/)（带 [Graph Data Frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes)）和 [Cosmos DB Spark 连接器](https://github.com/Azure/azure-cosmosdb-spark)。
空闲连接超时 | 1 小时 | Gremlin 服务将保持空闲 websocket 连接打开的时间量。 TCP 保持活动状态的数据包或 HTTP 保持活动状态的请求不会将连接生存期延长到超过此限制。 Cosmos DB Graph 引擎会将 websocket 连接视为空闲状态，前提是该连接上没有正在运行的活动 Gremlin 请求。
每小时的资源令牌 | **100** | Gremlin 客户端在连接到某个区域中的 Gremlin 帐户时使用的唯一资源令牌的数目。 当应用程序超过每小时唯一令牌限制时，将在下一次身份验证请求时返回 `"Exceeded allowed resource token limit of 100 that can be used concurrently"`。

## <a name="next-steps"></a>后续步骤
* [Azure Cosmos DB Gremlin 响应标头](gremlin-headers.md) 
* [使用 Gremlin 的 Azure Cosmos DB 资源令牌](how-to-use-resource-tokens-gremlin.md)
