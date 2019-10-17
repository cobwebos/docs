---
title: Azure Cosmos DB Gremlin 与 TinkerPop 功能的兼容性
description: 参考文档图引擎兼容性问题
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327031"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 兼容性
Azure Cosmos DB 图形引擎密切遵循[Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)遍历步骤规范，但也存在差异。

## <a name="behavior-differences"></a>行为差异

* Azure Cosmos DB Graph 引擎运行***广泛的第一次***遍历，而 TinkerPop Gremlin 是深度优先的。 此行为可在 Cosmos DB 的水平可伸缩系统中实现更好的性能。 

## <a name="unsupported-features"></a>不支持的功能

* ***[Gremlin 字节码](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是与编程语言无关的图遍历规范。 Cosmos DB Graph 尚不支持。 使用 ```GremlinClient.SubmitAsync()``` 并将遍历作为文本字符串传递。

* 目前不支持 ***@no__t 1***设置基数。 请改用 ```property(list, 'xyz', 1)```。

* ***```match()```*** 允许使用声明性模式匹配查询图形。 此功能不可用。

* 不支持将***对象作为属性***在顶点或边缘上。 属性只能是基元类型或数组。

* 不支持***按数组属性***```.order().by(<array property>)``` 进行排序。 只支持按基元类型排序。

* ***非基元 JSON 类型***不受支持。 使用 ```string```、```number``` 或 ```true``` @ no__t @ no__t-4 类型。 不支持 ```null``` 值。 

* ***GraphSONv3***序列化程序目前不可用。

* 由于系统的分布式性质，不支持***事务***。  在 Gremlin 帐户上配置适当的一致性模型以 "读取自己的写入"，并使用乐观并发解决冲突的写入。

## <a name="next-steps"></a>后续步骤
* 请访问[Cosmos DB 用户语音](https://feedback.azure.com/forums/263030-azure-cosmos-db)页面，分享反馈，帮助团队关注对你很重要的功能。
