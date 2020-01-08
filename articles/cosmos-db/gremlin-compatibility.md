---
title: Azure Cosmos DB Gremlin 与 TinkerPop 功能的兼容性
description: 参考文档图引擎兼容性问题
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 581bc813ca27067b1f27ab9866a45df3084dbbcc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644726"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 兼容性
Azure Cosmos DB 图形引擎密切遵循[Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)遍历步骤规范，但也存在差异。

## <a name="behavior-differences"></a>行为差异

* Azure Cosmos DB Graph 引擎运行***广泛的第一次***遍历，而 TinkerPop Gremlin 是深度优先的。 此行为可在 Cosmos DB 的水平可伸缩系统中实现更好的性能。 

## <a name="unsupported-features"></a>不支持的功能

* ***[Gremlin 字节码](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是与编程语言无关的图遍历规范。 Cosmos DB Graph 尚不支持。 使用 `GremlinClient.SubmitAsync()` 并将遍历作为文本字符串传递。

* 目前不支持***`property(set, 'xyz', 1)`*** 集基数。 请改用 `property(list, 'xyz', 1)`。 若要了解详细信息，请参阅[顶点 properties With TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)。

* ***`atch()`*** 允许使用声明性模式匹配查询图形。 此功能不可用。

* 不支持将***对象作为属性***在顶点或边缘上。 属性只能是基元类型或数组。

* 不支持***按数组属性进行排序***`order().by(<array property>)`。 只支持按基元类型排序。

* ***非基元 JSON 类型***不受支持。 使用 `string`、`number`或 `true`/`false` 类型。 不支持 `null` 值。 

* 当前不支持***GraphSONv3***序列化程序。 在连接配置中使用 `GraphSONv2` 序列化程序、读取器和编写器类。

* 当前不支持**Lambda 表达式和函数**。 这包括 `.map{<expression>}`、`.by{<expression>}`和 `.filter{<expression>}` 函数。 若要了解详细信息并了解如何使用 Gremlin 步骤重写它们，请参阅[lambda 上的说明](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)。

* 由于系统的分布式性质，不支持***事务***。  在 Gremlin 帐户上配置适当的一致性模型以 "读取自己的写入"，并使用乐观并发解决冲突的写入。

## <a name="next-steps"></a>后续步骤
* 请访问[Cosmos DB 用户语音](https://feedback.azure.com/forums/263030-azure-cosmos-db)页面，分享反馈，帮助团队关注对你很重要的功能。
