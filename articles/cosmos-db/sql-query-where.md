---
title: Azure Cosmos DB 中的 WHERE 子句
description: 了解 Azure Cosmos DB 的 SQL WHERE 子句
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "78898781"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 WHERE 子句

可选的 WHERE 子句 (`WHERE <filter_condition>`) 指定条件，查询只会将满足这些条件的源 JSON 项包含在结果中。 JSON 项必须将指定的条件评估为 `true` 才被视作结果。 索引层使用 WHERE 子句来确定可以作为结果的一部分的源项的最小子集。
  
## <a name="syntax"></a>语法
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>参数

- `<filter_condition>`  
  
   指定需要满足什么条件才会返回文档。  
  
- `<scalar_expression>`  
  
   表示待计算值的表达式。 有关详细信息，请参阅[标量表达式](sql-query-scalar-expressions.md)。  
  
## <a name="remarks"></a>备注
  
  指定为筛选条件的表达式的求值结果必须为 true，才会返回文档。 只有布尔值 `true` 会满足条件，其他任何值（undefined、null、false、数字、数组或对象）都不会满足条件。

  如果将分区键作为等式筛选器的一部分包含在 `WHERE` 子句中，则查询将仅自动筛选出相关分区。

## <a name="examples"></a>示例

以下查询请求包含值为 `AndersenFamily` 的 `id` 属性的项。 它会排除任何不带 `id` 属性或值与 `AndersenFamily` 不匹配的项。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

结果有：

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的标量表达式

上面的示例演示了一个简单的等式查询。 SQL API 还支持各种[标量表达式](sql-query-scalar-expressions.md)。 最常使用的是二进制和一元表达式。 来自源 JSON 对象的属性引用也是有效的表达式。

可以使用以下受支持的二元运算符：  

|**运算符类型**  | **值** |
|---------|---------|
|算术 | +,-,*,/,% |
|位    | \|、&、^、<<、>>、>>>（补零右移） |
|逻辑    | AND、OR、NOT      |
|比较 | =、!=、&lt;、&gt;、&lt;=、&gt;=、<> |
|String     |  \|\|（连接） |

以下查询使用二元运算符：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

还可以在查询中使用一元运算符 +、-、~ 和 NOT，如以下示例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

还可以在查询中使用属性引用。 例如，`SELECT * FROM Families f WHERE f.isRegistered` 返回包含值等于 `true` 的 `isRegistered` 属性的 JSON 项。 任何其他值（例如`false`、`null`、`Undefined`、`<number>`、`<string>`、`<object>` 或 `<array>`）会从结果中排除该项。

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [IN 关键字](sql-query-keywords.md#in)
- [FROM 子句](sql-query-from.md)