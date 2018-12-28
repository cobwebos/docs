---
title: Azure Cosmos DB 中的索引
description: 了解 Azure Cosmos DB 中索引的工作原理。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 0333bc5e95b74fc97cfff3d79adbe28aefff5d40
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834632"
---
# <a name="indexing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引

Azure Cosmos DB 是一种架构不可知的数据库，使你能够快速迭代应用程序，而无需处理架构或索引管理。 默认情况下，Azure Cosmos DB 自动对容器中的所有项编制索引，而无需来自开发人员的架构或辅助索引。

## <a name="items-as-trees"></a>树形式的项

通过将容器中的项投影为 JSON 文档并以树的形式表示，Azure Cosmos DB 将跨项的结构和实例值标准化为“动态编码的路径结构”统一概念 **。** 在此表示形式中，包含属性名称及其值的 JSON 文档中的每个标签都将成为树的节点。 树叶包含实际值，中间节点包含架构信息。 下图表示为容器中的两个项（1 和 2）创建的树：

![Azure Cosmos 容器中两个不同项的树表示形式](./media/index-overview/indexing-as-tree.png)

已创建伪根节点，作为对应于以下文档中标签的实际节点的父级。 嵌套数据结构驱动树中的层次结构。 标有数字值（例如，0、1、...）的中间人工节点用于表示枚举和数组索引。

## <a name="index-paths"></a>索引路径

Azure Cosmos DB 将项投影为 JSON 文档，将索引投影为树。 然后，可调整树中路径的策略。 可以选择在索引中包括或排除路径。 如果事先已知查询模式，这可以提高写入性能并减少方案所需的索引存储。 若要了解详细信息，请参阅[索引路径](index-paths.md)。

## <a name="indexing-under-the-hood"></a>索引：揭秘

Azure Cosmos 数据库将自动索引应用于数据，其中树中的所有路径均编制了索引，除非配置为排除某些路径。

Azure Cosmos 数据库采用倒排索引数据结构存储每个项的信息，以及促进用于查询的有效表示形式。 索引树是一种表示容器中各项的所有树联合构造的文档。 当在容器中添加新项或更新现有项时，索引树会随时间的推移成长。 不同于关系数据库索引，在向现有结构引入新字段和添加新项时，Azure Cosmos DB 不会重新从头开始编制索引。 

索引树的每个节点都是索引条目，包含标签和位置值（称为术语）以及项 ID（称为发布）。 倒排索引图中大括号中的发布（例如 {1,2}）对应于包含给定标签值的项（例如 Document1 和 Document2）。 统一处理架构标签和实例值的重要意义在于所有内容都打包在较大的索引中。 仍在叶中的实例值不会重复，该值可位于各项中的不同角色中，并且可附带不同架构标签，但它是相同的值。 下图显示不同项的倒排索引：

![深入了解索引、倒排索引](./media/index-overview/inverted-index.png)

> [!NOTE]
> 倒排索引似乎类似于在信息检索域的搜索引擎中使用的索引结构。 凭借此方法，Azure Cosmos DB 允许搜索数据库中的任何项，而无需考虑其架构结构。

对于标准化路径，该索引将从根到值编码转发路径，以及值的类型信息。 对路径和值进行编码，以提供各种类型的索引，例如范围、空间种类。 值编码旨在提供唯一值或一组路径的组合。

## <a name="querying-with-indexes"></a>使用索引进行查询

倒排索引允许查询快速标识匹配查询谓词的文档。 通过在路径方面统一处理架构和实例值，倒排索引也是树。 因此，可将索引和结果序列化为有效 JSON 文档，并在树表示形式中返回时作为文档本身返回。 此方法支持通过其他查询的结果进行递归。 下图是点查询中的索引示例：  

![点查询示例](./media/index-overview/index-point-query.png)

对于范围查询，GermanTax 是用户定义的函数，作为查询处理的一部分执行。 用户定义的函数是可以提供与查询集成的丰富编程逻辑的任何已注册 Javascript 函数。 下图是范围查询中的索引示例：

![范围查询示例](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引策略](index-policy.md)
- [索引类型](index-types.md)
- [索引路径](index-paths.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
