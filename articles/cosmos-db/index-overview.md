---
title: Azure Cosmos DB 中的索引
description: 了解 Azure Cosmos DB 中索引的工作原理。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: ecf53251020ce1b639a5bf8da65f5d31ff699db9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265689"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的索引 - 概述

Azure Cosmos DB 是一种架构不可知的数据库，使你能够快速迭代应用程序，而无需处理架构或索引管理。 默认情况下，Azure Cosmos DB 自动对容器中的所有项编制索引，而无需来自开发人员的架构或辅助索引。

## <a name="items-as-trees"></a>树形式的项

通过投影为 JSON 文档在容器中的项，并代表它们树，Azure Cosmos DB 会将标准化的结构和实例值项为统一的概念**动态编码路径结构**. 在这种表示形式，在 JSON 文档，其中包含属性名称及其值，每个标签将成为树的节点。 在树的叶包含实际值和中间节点包含的架构信息。 下图表示两个创建的树中的 Azure Cosmos 容器的项 （1 和 2）：

![Azure Cosmos 容器中两个不同项的树表示形式](./media/index-overview/indexing-as-tree.png)

伪根节点创建为对应于下面的 JSON 文档中的标签的实际节点的父级。 嵌套数据结构驱动树中的层次结构。 标有数字值（例如，0、1、...）的中间人工节点用于表示枚举和数组索引。

## <a name="index-paths"></a>索引路径

Azure Cosmos DB 项目项中的 Azure Cosmos 容器作为 JSON 文档和索引为树。 然后，您可以调整树中路径的索引策略。 可以选择在索引中包括或排除路径。 如果事先已知查询模式，这可以提高写入性能并减少方案所需的索引存储。 若要了解详细信息，请参阅[索引路径](index-paths.md)。

## <a name="indexing-under-the-hood"></a>索引：揭秘

Azure Cosmos 数据库适用*自动索引编制*到数据，其中树中的每个路径编制了索引，除非你配置它们以排除某些路径。

Azure Cosmos 数据库采用倒排索引数据结构存储每个项的信息，以及促进用于查询的有效表示形式。 在索引树是使用所有表示容器中的各个项的树的联合构造的文档。 在索引树会随着，添加新项或在容器中更新现有项。 不同于关系数据库索引编制，Azure Cosmos DB 不会重启索引从零开始，引入新的字段。 新项将添加到现有的索引结构。 

在索引树的每个节点都包含名为的标签和位置值的索引条目*术语*，和的项，称为 Id*帖子*。 在大括号中的发布内容 (例如{1,2}) 在倒排的索引图中的项对应如*Document1*并*Document2*包含给定的标签值。 统一处理架构标签和实例值的重要意义是所有内容打包内较大的索引。 仍在叶中的实例值不会重复，该值可位于各项中的不同角色中，并且可附带不同架构标签，但它是相同的值。 下图显示了两个不同项的倒排索引：

![深入了解索引、倒排索引](./media/index-overview/inverted-index.png)

> [!NOTE]
> 倒排索引似乎类似于在信息检索域的搜索引擎中使用的索引结构。 凭借此方法，Azure Cosmos DB 允许搜索数据库中的任何项，而无需考虑其架构结构。

对于标准化路径，该索引将从根到值编码转发路径，以及值的类型信息。 路径和值进行编码，提供各种类型的等范围，空间索引。值编码旨在提供唯一值或一组路径的组合。

## <a name="querying-with-indexes"></a>使用索引进行查询

倒排索引允许查询快速标识匹配查询谓词的文档。 通过将架构和实例的值在路径方面均衡，倒排的索引也是一个树。 因此，可将索引和结果序列化为有效 JSON 文档，并在树表示形式中返回时作为文档本身返回。 此方法支持通过其他查询的结果进行递归。 下图是点查询中的索引示例：  

![点查询示例](./media/index-overview/index-point-query.png)

对于范围查询， *GermanTax*是[用户定义函数](stored-procedures-triggers-udfs.md#udfs)作为查询处理的一部分执行。 用户定义函数是可以提供丰富的编程逻辑集成到查询任何已注册，JavaScript 函数。 下图是范围查询中的索引示例：

![范围查询示例](./media/index-overview/index-range-query.png)

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引编制策略](index-policy.md)
- [索引类型](index-types.md)
- [索引路径](index-paths.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
