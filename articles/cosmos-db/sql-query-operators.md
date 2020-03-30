---
title: Azure Cosmos DB 的 SQL 查询运算符
description: 了解 Azure Cosmos DB 支持的 SQL 运算符，例如相等、比较和逻辑运算符。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063576"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的运算符

本文详细介绍 Azure Cosmos DB 支持的各种运算符。

## <a name="equality-and-comparison-operators"></a>相等和比较运算符

下表显示了 SQL API 中任意两个 JSON 类型之间等式比较的结果。

| **操作** | **定义** | **空** | **布尔** | **数量** | **字符串** | **对象** | 数组**** |
|---|---|---|---|---|---|---|---|
| **定义** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **空** | Undefined | **还行** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布尔** | Undefined | Undefined | **还行** | Undefined | Undefined | Undefined | Undefined |
| **数量** | Undefined | Undefined | Undefined | **还行** | Undefined | Undefined | Undefined |
| **字符串** | Undefined | Undefined | Undefined | Undefined | **还行** | Undefined | Undefined |
| **对象** | Undefined | Undefined | Undefined | Undefined | Undefined | **还行** | Undefined |
| 数组**** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **还行** |

对于 `>`、`>=`、`!=`、`<` 和 `<=` 等比较运算符，跨类型的比较或者两个对象或数组之间的比较会生成 `Undefined`。  

如果标量表达式的结果为 `Undefined`，则不会将该项包含在结果中，因为 `Undefined` 不等于 `true`。

## <a name="logical-and-or-and-not-operators"></a>逻辑（AND、OR 和 NOT）运算符

逻辑运算符对布尔值进行运算。 下表显示了这些运算符的逻辑真值表：

**OR 运算符**

当`true`任一条件为`true`时返回 。

|  | **True** | **False** | **定义** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Undefined |
| **定义** |True |Undefined |Undefined |

**和运算符**

当`true`两个表达式都是`true`时返回。

|  | **True** | **False** | **定义** |
| --- | --- | --- | --- |
| **True** |True |False |Undefined |
| **False** |False |False |False |
| **定义** |Undefined |False |Undefined |

**不接线员**

反转任何布尔表达式的值。

|  | **不** |
| --- | --- |
| **True** |False |
| **False** |True |
| **定义** |Undefined |

**运算符优先级**

逻辑运算符`OR`、`AND`和`NOT`具有以下优先级显示：

| **运算符** | **优先** |
| --- | --- |
| **不** |1 |
| **和** |2 |
| **或** |3 |

## <a name="-operator"></a>* 运算符

特殊运算符 * 按原样投影整个项。 在使用时，它必须仅为投影的字段。 类似于 `SELECT * FROM Families f` 的查询是有效的，而 `SELECT VALUE * FROM Families f` 和 `SELECT *, f.id FROM Families f` 是无效的。

## <a name="-and--operators"></a>? 和 ?? 运算符

如同在 C# 和 JavaScript 等编程语言中那样，可以使用三元 (?) 和联合 (??) 运算符来生成条件表达式。

可以使用 ? 运算符即时构造新的 JSON 属性。 例如，以下查询将年级分类为 `elementary` 或 `other`：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

还可以将调用嵌套到 ? 运算符，如以下查询中所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

与其他查询运算符一样， 如果引用的属性缺失或者要比较的类型不同，则 ? 运算符将会排除项。

查询半结构化 或混合类型的数据时，可以使用 ?? 运算符有效地检查项中的属性。 例如，以下查询返回 `lastName`（如果存在）或 `surname`（如果 `lastName` 不存在）。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [关键字](sql-query-keywords.md)
- [选择子句](sql-query-select.md)
