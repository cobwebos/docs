---
title: Azure Cosmos DB 中的 OFFSET LIMIT 子句
description: 了解在 Azure Cosmos DB 中进行查询时如何使用 OFFSET LIMIT 子句跳过某些特定值和采用某些特定值
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76771569"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 OFFSET LIMIT 子句

OFFSET LIMIT 子句是一个可选子句，可以跳过它，然后从查询中获取一些值。 必须在 OFFSET LIMIT 子句中指定 OFFSET 计数和 LIMIT 计数。

将 OFFSET LIMIT 与 ORDER BY 子句结合使用时，将通过跳过然后提取排序值来生成结果集。 如果不使用 ORDER BY 子句，则会生成值的确定顺序。

## <a name="syntax"></a>语法
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>参数

- `<offset_amount>`

   指定查询结果应跳过的项数（整数）。

- `<limit_amount>`
  
   指定查询结果应包含的项数（整数）

## <a name="remarks"></a>备注
  
  必须在 `OFFSET` 子句中同时指定 `LIMIT` 计数和 `OFFSET LIMIT` 计数。 如果使用可选的 `ORDER BY` 子句，将会通过跳过排序值来生成结果集。 否则，查询将返回固定顺序的值。

  使用 `OFFSET LIMIT` 进行查询时的 RU 费用会随着要跳过的字词数量的增加而增加。 对于有多个结果页的查询，我们通常建议使用继续标记。 继续标记是一个“书签”，用于在以后恢复查询。 如果使用 `OFFSET LIMIT`，则没有“书签”。 如果希望返回查询的下一页，则必须从头开始。
  
  如果希望完全跳过文档并保存客户端资源，则应使用 `OFFSET LIMIT`。 例如，如果要跳到第 1000 个查询结果，无需查看第 1 到第 999 个结果，则应使用 `OFFSET LIMIT`。 在后端，`OFFSET LIMIT` 仍会加载每个文档，包括那些跳过的文档。 性能优势是，通过避免处理不需要的文档，可以节省客户端资源。

## <a name="examples"></a>示例

例如，以下查询跳过第一个值并返回第二个值（按居住城市名称的顺序）：

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

以下查询跳过第一个值并返回第二个值（不排序）：

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
