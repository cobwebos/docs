---
title: Azure Cosmos DB 的 SQL 关键字
description: 了解 Azure Cosmos DB 的 SQL 关键字。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81261561"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的关键字

本文详细介绍可在 Azure Cosmos DB SQL 查询中使用的关键字。

## <a name="between"></a>BETWEEN

可以使用 `BETWEEN` 关键字来表达针对字符串或数值范围的查询。 例如，以下查询返回其中第一个孩子的年级为 1-5（含）的所有项。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

还可以在 `SELECT` 子句中使用 `BETWEEN` 关键字，如以下示例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

与 ANSI SQL 不同，在 SQL API 中，可以针对混合类型的属性表达范围查询。 例如，在某些项中，`grade` 可能是类似于 `5` 的数字；而在其他一些项中，它可能是类似于 `grade4` 的字符串。 在这些情况下（与在 JavaScript 中一样），两个不同类型之间的比较会生成 `Undefined`，因此会跳过该项。

> [!TIP]
> 为了更快地执行查询，请创建一个索引策略，该策略对 `BETWEEN` 子句筛选的任何数值属性或路径使用范围索引类型。

## <a name="distinct"></a>DISTINCT

`DISTINCT` 关键字可消除查询投影中的重复项。

在此示例中，查询将投影每个姓氏的值：

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

结果有：

```json
[
    "Andersen"
]
```

也可以投影唯一对象。 在本例中，两个文档中的一个文档不存在 lastName 对象，因此查询将返回一个空对象。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

结果有：

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

还可以在子查询内的投影中使用 DISTINCT：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查询投影包含每个孩子的 givenName 的数组，并删除了重复项。 此数组的别名为 ChildNames，并在外部查询中投影。

结果有：

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

不支持查询包含使用 `DISTINCT` 的聚合系统函数和子查询。 例如，不支持以下查询：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

使用 IN 关键字可以检查指定的值是否与列表中的任一值匹配。 例如，以下查询返回 `id` 为 `WakefieldFamily` 或 `AndersenFamily` 的所有家庭项。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

以下示例返回状态为任何指定值的所有项：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API 支持[循环访问 JSON 数组](sql-query-object-array.md#Iteration)，它可以通过 FROM 源中的 IN 关键字添加一个新的构造。

如果在 `IN` 筛选器中包含分区键，则查询会自动地仅筛选出相关分区。

## <a name="top"></a>TOP

TOP 关键字以未定义的顺序返回前 `N` 个查询结果。 最佳做法是将 TOP 与 `ORDER BY` 子句配合使用，将结果限制为前 `N` 个有序值。 要预见性地指示哪些行受到 TOP 的影响，只能结合使用这两个子句。

可以结合一个常量值使用 TOP（如以下示例中所示），或者在参数化查询中结合一个变量值使用 TOP。

```sql
    SELECT TOP 1 *
    FROM Families f
```

结果有：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [联接](sql-query-join.md)
- [子查询](sql-query-subquery.md)