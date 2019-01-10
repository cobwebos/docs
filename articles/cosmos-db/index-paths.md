---
title: 使用 Azure Cosmos DB 中的索引路径
description: Azure Cosmos DB 中的索引路径概述
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: c22d8d69284c546a4fccc86302672d81ce65b9e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032765"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引路径

通过使用 Azure Cosmos DB 中的索引路径，你可以选择在索引编制中包括或排除特定的路径。 在知道查询模式的情况下，选择特定的路径可以提高写入性能并减小索引存储。 索引路径以根 (`/`) 通配符运算符开头，并且通常以 `?` 通配符运算符结尾。 此模式表示前缀存在多个可能的值。 例如，若要为查询 `SELECT * FROM Families F WHERE F.familyName = "Andersen"` 提供服务，必须在容器的索引策略中包括 `/familyName/?` 的索引路径。

索引路径还可以使用 `*` 通配符运算符以递归方式指定路径在前缀下的行为。 例如，`/payload/*` 可用于从索引编制中排除有效负载属性下的所有内容。

## <a name="common-patterns-for-index-paths"></a>索引路径的常见模式

下面是用于指定索引路径的常用模式︰

| **路径** | **说明/用例** |
| ---------- | ------- |
| /   | 集合的默认路径。 递归并应用于整个文档树。|
| /prop/?  | 执行查询所需的索引路径（分别为哈希或范围类型）：<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*  | 指定标签下所有路径的索引路径。 使用以下查询<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?  | 对标量数组（如 ["a"、"b"、"c"]）进行迭代和 JOIN 查询所需的索引路径︰<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5  |
| /props/[]/subprop/? | 针对诸如 [{subprop:"a"}, {subprop:"b"}] 的对象数组进行迭代和 JOIN 查询所需的索引路径：<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | 进行查询所需的索引路径（分别为哈希或范围类型）：<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

在设置自定义索引路径时，需要为由特殊路径 `/*` 表示的整个项指定默认的索引编制规则。

## <a name="next-steps"></a>后续步骤

阅读以下文章，详细了解 Azure Cosmos DB 中的索引编制：

- [索引编制概述](index-overview.md)
- [Azure Cosmos DB 中的索引编制策略](indexing-policies.md)
- [Azure Cosmos DB 中的索引类型](index-types.md)
