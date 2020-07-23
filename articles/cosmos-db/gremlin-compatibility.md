---
title: Azure Cosmos DB Gremlin 与 TinkerPop 功能的兼容性
description: 参考文档 Graph 引擎兼容性问题
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 1db7937cb574ce62986f25e0bfa688dc54b5c606
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700593"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 兼容性
Azure Cosmos DB Graph 引擎严格遵循 [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) 遍历步骤规范，但在实现中存在特定于 Azure Cosmos DB 的差异。 若要查看支持的 Gremlin 步骤列表，请参阅 [Gremlin API 线路协议支持](gremlin-support.md)一文。

## <a name="behavior-differences"></a>行为差异

* Azure Cosmos DB Graph 引擎运行***广度优先***遍历，而 TinkerPop Gremlin 则深度优先。 这种行为在像 Cosmos DB 这样的水平可缩放系统中可实现更好的性能。 

## <a name="unsupported-features"></a>不支持的功能

* ***[Gremlin 字节码](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是与编程语言无关的图遍历规范。 Cosmos DB Graph 尚不支持它。 请使用 `GremlinClient.SubmitAsync()` 并以文本字符串的形式传递遍历。

* ***`property(set, 'xyz', 1)`*** 目前不支持集基数。 请改用 `property(list, 'xyz', 1)`。 若要了解详细信息，请参阅 [TinkerPop 的顶点属性](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)。

* `match()` 步骤当前不可用。 此步骤提供声明性查询功能。

* 不支持顶点或边上的***对象作为属性***。 属性只能是基元类型或数组。

* 不支持***按数组属性排序*** `order().by(<array property>)`。 只支持按基元类型排序。

* 不支持***非基元 JSON 类型***。 使用 `string`、`number` 或 `true`/`false` 类型。 不支持 `null` 值。 

* 目前不支持 ***GraphSONv3*** 序列化程序。 在连接配置中使用 `GraphSONv2` Serializer、Reader 和 Writer 类。 Azure Cosmos DB Gremlin API 返回的结果的格式与 GraphSON 格式不同。 

* 目前不支持 **Lambda 表达式和函数**。 这包括 `.map{<expression>}`、`.by{<expression>}` 和 `.filter{<expression>}` 函数。 若要了解详细信息，并了解如何使用 Gremlin 步骤重写这些函数，请参阅[关于 Lambda 的说明](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)。

* 由于系统具有分布式特性，因此***事务***不受支持。  在 Gremlin 帐户上配置适当的一致性模型以“读取自己的写入”，并使用乐观并发解决冲突的写入。

## <a name="known-limitations"></a>已知的限制

* **使用中间遍历 `.V()` 步骤的 Gremlin 查询的索引利用**：目前，只有遍历的第一次 `.V()` 调用将使用索引来解析附加到它的任何筛选器或谓词。 后续调用将不会访问索引，因为这可能会增加查询的延迟和成本。
    
    假设使用默认索引，以 `.V()` 步骤开始的典型读取 Gremlin 查询将在其附加的筛选步骤中使用参数，例如 `.has()` 或 `.where()`，以优化查询的成本和性能。 例如：

    ```java
    g.V().has('category', 'A')
    ```

    但是，当 Gremlin 查询中包含多个 `.V()` 步骤时，查询的数据解析可能达不到最优效果。 以下列查询为例：

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    此查询将根据名为 `category` 的属性返回两组顶点。 在这种情况下，只有第一次调用 `g.V().has('category', 'A')` 将使用索引根据其属性值解析顶点。

    对于此查询，一个解决方法是使用 `.map()` 和 `union()` 等子遍历步骤。 下面来举例说明：

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    可以使用 [Gremlin `executionProfile()` 步骤](graph-execution-profile.md 检查查询的性能。

## <a name="next-steps"></a>后续步骤
* 访问 [Cosmos DB 用户之声](https://feedback.azure.com/forums/263030-azure-cosmos-db)页以共享反馈并帮助团队专注于对你重要的功能。
