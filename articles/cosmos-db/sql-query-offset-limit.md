---
title: Azure Cosmos DB 中的偏移限制子句
description: 了解如何在中查询时使用 OFFSET LIMIT 子句跳过并采用某些值 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771569"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的偏移限制子句

OFFSET LIMIT 子句是一个可选子句，用于跳过查询中的一些值。 偏移量限制和限制计数在偏移限制子句中是必需的。

当 OFFSET 限制与 ORDER BY 子句结合使用时，将通过执行 skip 并采用有序值生成结果集。 如果未使用 ORDER BY 子句，则将导致值的确定性顺序。

## <a name="syntax"></a>语法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>参数

- `<offset_amount>`

   指定查询结果应跳过的项数。

- `<limit_amount>`
  
   指定查询结果应包含的项的整数数量

## <a name="remarks"></a>备注
  
  `OFFSET LIMIT` 子句中需要 `OFFSET` 计数和 `LIMIT` 计数。 如果使用了可选的 `ORDER BY` 子句，则通过跳过有序值来生成结果集。 否则，查询将返回固定的值顺序。

  与 `OFFSET LIMIT` 的查询的 RU 费用将随着要增加的字词数量的增加而增加。 对于具有多个结果页的查询，我们通常建议使用继续标记。 继续标记是一个 "书签"，用于查找以后可恢复查询的位置。 如果使用 `OFFSET LIMIT`，则没有 "书签"。 如果希望返回查询的下一页，则必须从头开始。
  
  如果希望完全跳过文档并保存客户端资源，则应使用 `OFFSET LIMIT`。 例如，如果要跳到1000th 查询结果，而无需查看结果1到999，则应使用 `OFFSET LIMIT`。 在后端上，`OFFSET LIMIT` 仍会加载每个文档，包括跳过的文档。 通过避免处理不需要的文档，可节省客户端资源。

## <a name="examples"></a>示例

例如，下面的查询将跳过第一个值，并返回第二个值（按居民姓名的顺序）：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

结果有：

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

下面的查询将跳过第一个值，并返回第二个值（不排序）：

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

结果有：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [ORDER BY 子句](sql-query-order-by.md)
