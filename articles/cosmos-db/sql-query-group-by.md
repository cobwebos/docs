---
title: Azure Cosmos DB 中的 GROUP BY 子句
description: 了解 Azure Cosmos DB 的 GROUP BY 子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819109"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 GROUP BY 子句

GROUP BY 子句按照一个或多个指定属性的值来拆分查询的结果。

> [!NOTE]
> Azure Cosmos DB 当前在 .NET SDK 3.3 及更高版本以及 JavaScript SDK 3.4 及更高版本中支持 GROUP BY。
> 对其他语言 SDK 的支持目前不可用，但已计划。

## <a name="syntax"></a>语法

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>参数

- `<scalar_expression_list>`

   指定将要用来拆分查询结果的表达式。

- `<scalar_expression>`
  
   任何标量表达式都是允许的，但标量子查询和标量聚合除外。 每个标量表达式必须包含至少一个属性引用。 单个表达式的数目或每个表达式的多重性没有限制。

## <a name="remarks"></a>备注
  
  当查询使用 GROUP BY 子句时，SELECT 子句只能包含包括在 GROUP BY 子句中的属性和系统函数的一部分。 一个例外是[聚合系统函数](sql-query-aggregates.md)，此类函数可以出现在 SELECT 子句中，但不需包含在 GROUP BY 子句中。 也可始终在 SELECT 子句中包含文本值。

  GROUP BY 子句必须位于 SELECT、FROM 和 WHERE 子句后面，OFFSET LIMIT 子句前面。 目前不能将 GROUP BY 和 ORDER BY 子句配合使用，但这已在计划内。

  GROUP BY 子句不允许下述任何项：
  
- 别名属性或别名系统函数（在 SELECT 子句中，别名仍然是允许的）
- 子查询
- 聚合系统函数（在 SELECT 子句中，仍然允许这些函数）

## <a name="examples"></a>示例

这些示例使用可以通过 [Azure Cosmos DB 查询操场](https://www.documentdb.com/sql/demo)获取的营养数据集。

例如，下面这个查询返回每个 foodGroup 中项的总计数：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

部分结果如下（使用了 TOP 关键字来限制结果）：

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

此查询有两个表达式，用于拆分结果：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

部分结果如下：

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

此查询在 GROUP BY 子句中有一个系统函数：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

部分结果如下：

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

此查询在项属性表达式中使用关键字和系统函数：

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

其结果是：

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [聚合函数](sql-query-aggregates.md)
