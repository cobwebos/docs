---
title: Azure Cosmos DB 中的偏移限制子句
description: 了解 Azure Cosmos DB 的偏移限制子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326872"
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
  
  偏移量限制子句和限制计数都是必需的。 如果使用了可选的 @no__t 0 子句，则通过跳过有序值来生成结果集。 否则，查询将返回固定的值顺序。 目前，只有单个分区中的查询支持此子句，跨分区查询尚不支持。

## <a name="examples"></a>示例

例如，下面的查询将跳过第一个值，并返回第二个值（按居民姓名的顺序）：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

其结果是：

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

其结果是：

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
