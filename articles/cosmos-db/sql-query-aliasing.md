---
title: Azure Cosmos DB 中的别名
description: 了解如何在 Azure Cosmos DB SQL 查询中使用别名来区分具有相同名称的两个属性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873465"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB 中的别名

可以在查询中显式给值别名。 如果查询包含两个具有相同名称的属性，请使用别名重命名其中一个属性或两个属性，使其在预计的结果中消除歧义。

## <a name="examples"></a>示例

用于别名的 AS 关键字是可选的，如下面的示例中将第二个值投影为 `NameInfo`：

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

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
- [FROM 子句](sql-query-from.md)
