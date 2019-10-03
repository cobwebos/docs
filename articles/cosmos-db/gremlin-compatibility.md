---
title: Azure Cosmos DB Gremlin 与 TinkerPop 功能的兼容性
description: 参考文档图引擎兼容性问题
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911018"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin 兼容性
Azure Cosmos DB 图形引擎密切遵循[Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps)遍历步骤规范，但也存在差异。

## <a name="behavior-differences"></a>行为差异

* Azure Cosmos DB Graph 引擎运行***广泛的第一次***遍历，而 TinkerPop Gremlin 是深度优先的。 此行为可在 Cosmos DB 的水平可伸缩系统中实现更好的性能。 

## <a name="unsupported-features"></a>不受支持的功能

* ***[Gremlin 字节码](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** 是与编程语言无关的图遍历规范。 Cosmos DB Graph 尚不支持。 使用```GremlinClient.SubmitAsync()```并将遍历作为文本字符串传递。

* ***```property(set, 'xyz', 1)```*** 当前不支持设置基数。 请改用 ```property(list, 'xyz', 1)```。

* ***```match()```*** 允许使用声明性模式匹配查询关系图。 此功能不可用。

* 不支持将***对象作为属性***在顶点或边缘上。 属性只能是基元类型或数组。

* ***按数组属性排序***```.order().by(<array property>)```不受支持。 只支持按基元类型排序。

* ***非基元 JSON 类型***不受支持。 使用```string```、 ```number```或```true``` 类型。/ ```false``` ```null```值不受支持。 

* ***GraphSONv3***序列化程序目前不可用。

* 由于系统的分布式性质，不支持***事务***。  在 Gremlin 帐户上配置适当的一致性模型以 "读取自己的写入"，并使用乐观并发解决冲突的写入。

## <a name="next-steps"></a>后续步骤
* 请访问[Cosmos DB 用户语音](https://feedback.azure.com/forums/263030-azure-cosmos-db)页面，分享反馈，帮助团队关注对你很重要的功能。
