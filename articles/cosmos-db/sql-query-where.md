---
title: Azure Cosmos DB 中的 WHERE 子句
description: 了解 Azure Cosmos DB 的 SQL WHERE 子句
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326646"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 WHERE 子句

可选 WHERE 子句（`WHERE <filter_condition>`）指定源 JSON 项必须满足的条件，以便查询将它们包含在结果中。 JSON 项必须将指定的条件评估为 `true` 才能视为结果。 索引层使用 WHERE 子句来确定可以作为结果的一部分的源项的最小子集。
  
## <a name="syntax"></a>语法
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>参数

- `<filter_condition>`  
  
   指定返回的文档需满足的条件。  
  
- `<scalar_expression>`  
  
   表示要计算的值的表达式。 有关详细信息，请参阅[标量表达式](sql-query-scalar-expressions.md)。  
  

## <a name="remarks"></a>备注
  
  为了能够返回文档，指定为筛选条件的表达式的求值结果必须为 true。 只有当布尔值为 true 才可以满足条件，任何其他值：未定义、null、false、数字、数组或对象均不满足条件。 

## <a name="examples"></a>示例

以下查询请求包含值为 @no__t 的 @no__t 0 属性的项。 它排除没有 @no__t 值属性或值与 `AndersenFamily` 不匹配的任何项。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

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

您可以使用以下受支持的二进制运算符：  

|**运算符类型**  | **值** |
|---------|---------|
|算术 | +,-,*,/,% |
|位    | \|、&、^、<<、>>、>>>（补零右移） |
|逻辑    | AND、OR、NOT      |
|比较 | =、!=、&lt;、&gt;、&lt;=、&gt;=、<> |
|字符串     |  \|\|（连接） |

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

你还可以使用一元运算符 +,-, ~，而不能在查询中使用，如以下示例中所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

您还可以在查询中使用属性引用。 例如，`SELECT * FROM Families f WHERE f.isRegistered` 返回的 JSON 项包含属性 `isRegistered`，其值等于 `true`。 任何其他值（例如 `false`、`null`、`Undefined`、`<number>`、@no__t、@no__t 或 @no__t）都将从结果中排除项。 

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM 子句](sql-query-from.md)