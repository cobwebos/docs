---
title: Azure Cosmos DB 的 SQL 查询运算符
description: 了解 Azure Cosmos DB 支持的相等性、比较和逻辑运算符等 SQL 运算符。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870932"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 中的运算符

本文详细介绍 Azure Cosmos DB 支持的各种运算符。

## <a name="equality-and-comparison-operators"></a>相等和比较运算符

下表显示了 SQL API 中任意两个 JSON 类型之间等式比较的结果。

| **Op** | 未定义 | Null | **布尔值** | **Number** | **字符串** | **Object** | 数组 |
|---|---|---|---|---|---|---|---|
| 未定义 | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| Null | Undefined | **正常** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **布尔值** | Undefined | Undefined | **正常** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **正常** | Undefined | Undefined | Undefined |
| **字符串** | Undefined | Undefined | Undefined | Undefined | **正常** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **正常** | Undefined |
| 数组 | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **正常** |

对于 `>`、`>=`、`!=`、`<`和 `<=`等比较运算符，在类型之间或在两个对象或数组之间进行比较会产生 `Undefined`。  

如果 `Undefined`标量表达式的结果，则不会在结果中包括该项，因为 `Undefined` 不等于 `true`。

## <a name="logical-and-or-and-not-operators"></a>逻辑（AND、OR 和 NOT）运算符

逻辑运算符对布尔值进行运算。 下表显示了这些运算符的逻辑事实数据表：

**OR 运算符**

| 或 | 正确 | 错误 | Undefined |
| --- | --- | --- | --- |
| 正确 |正确 |正确 |正确 |
| 错误 |正确 |错误 |Undefined |
| Undefined |正确 |Undefined |Undefined |

**AND 运算符**

| AND | 正确 | 错误 | Undefined |
| --- | --- | --- | --- |
| 正确 |正确 |错误 |Undefined |
| 错误 |错误 |错误 |错误 |
| Undefined |Undefined |错误 |Undefined |

**NOT 运算符**

| NOT |  |
| --- | --- |
| 正确 |错误 |
| 错误 |正确 |
| Undefined |Undefined |


## <a name="-operator"></a>* 运算符

特殊运算符 * 将整个项按原样投影。 在使用时，它必须仅为投影的字段。 `SELECT * FROM Families f` 的查询有效，但 `SELECT VALUE * FROM Families f` 和 `SELECT *, f.id FROM Families f` 无效。

## <a name="-and--operators"></a>? 和？？ 運算子

您可以使用三元（？）和联合（？？）运算符来生成条件表达式，就像和 JavaScript 之类C#的编程语言一样。 

您可以使用？ 用于动态构造新的 JSON 属性的运算符。 例如，以下查询将评分级别分类为 `elementary` 或 `other`：

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

你还可以将调用嵌套到？ 运算符，如以下查询所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

与其他查询运算符一样，？ 如果缺少引用的属性或要比较的类型不同，则运算符会排除项。

使用 "？" 运算符可在针对半结构化数据或混合类型数据进行查询时有效地检查项中的属性。 例如，下面的查询将返回 `lastName` （如果存在）或 `surname` （如果 `lastName` 不存在）。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [关键字](sql-query-keywords.md)
- [SELECT 子句](sql-query-select.md)
