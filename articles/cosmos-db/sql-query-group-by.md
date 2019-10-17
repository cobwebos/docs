---
title: Azure Cosmos DB 中的 GROUP BY 子句
description: 了解 Azure Cosmos DB 的 GROUP BY 子句。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: d92e24836a0eb5757de9bbdb516be290456deb7f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333256"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 GROUP BY 子句

GROUP BY 子句根据一个或多个指定属性的值来划分查询的结果。

> [!NOTE]
> Azure Cosmos DB 当前在[.NET SDK 3.3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.3.0)或更高版本中支持 GROUP BY。
> 对其他语言 SDK 和 Azure 门户的支持目前不可用，但已计划。

## <a name="syntax"></a>语法

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>参数

- `<scalar_expression_list>`

   指定将用于划分查询结果的表达式。

- `<scalar_expression>`
  
   除标量子查询和标量聚合外，允许使用任何标量表达式。 每个标量表达式必须至少包含一个属性引用。 单个表达式的数目或每个表达式的基数没有限制。

## <a name="remarks"></a>备注
  
  当查询使用 GROUP BY 子句时，SELECT 子句只能包含 GROUP BY 子句中包含的属性和系统函数的子集。 [聚合系统函数](sql-query-aggregates.md)是一个例外，它可以出现在 SELECT 子句中，而不包括在 group by 子句中。 您还可以在 SELECT 子句中始终包含文字值。

  GROUP BY 子句必须位于 SELECT、FROM 和 WHERE 子句之后以及 OFFSET LIMIT 子句之前。 当前无法将 GROUP BY 与 ORDER BY 子句一起使用，但这是计划的。

  GROUP BY 子句不允许下列任何内容：
  
- 对属性或别名系统函数进行别名（SELECT 子句中仍允许使用别名）
- 个子
- 聚合系统函数（仅在 SELECT 子句中允许）

## <a name="examples"></a>示例

这些示例使用营养数据集，通过[Azure Cosmos DB 查询操场](https://www.documentdb.com/sql/demo)提供。

例如，下面的查询返回每个 foodGroup 中的总项数：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

某些结果为（TOP 关键字用于限制结果）：

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

此查询有两个用于划分结果的表达式：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

结果如下：

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

此查询在 GROUP BY 子句中具有系统函数：

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

结果如下：

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

此查询在 item 属性表达式中使用关键字和系统函数：

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
