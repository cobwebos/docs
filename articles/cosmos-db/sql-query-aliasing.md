---
title: Azure Cosmos DB 中的别名
description: 了解如何在 Azure Cosmos DB SQL 查询中的别名值
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342580"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的别名

可以显式为查询中的值指定别名。 如果查询包含两个同名的属性，请使用别名来重命名其中一个或两个属性，以便可以在投影的结果中消除其歧义。

## <a name="examples"></a>示例

如以下示例所示，将第二个值投影为 `NameInfo` 时，用于别名的 AS 关键字是可选的：

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 子句](sql-query-select.md)
- [FROM 子句](sql-query-from.md)
