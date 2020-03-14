---
title: Azure Cosmos DB 的 SQL 关键字
description: 了解 Azure Cosmos DB 的 SQL 关键字。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 711e961bd5eb1607e2e6f11b0b5762423d78c0e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246573"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB 中的关键字
本文详细介绍了可在 Azure Cosmos DB SQL 查询中使用的关键字。

## <a name="between"></a>BETWEEN

与在 ANSI SQL 中一样，您可以使用 BETWEEN 关键字来针对字符串或数值范围表达查询。 例如，以下查询将返回第一个子级别为1-5 （含）的所有项。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

与在 ANSI SQL 中不同，还可以使用 FROM 子句中的 BETWEEN 子句，如以下示例中所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

与 ANSI SQL 不同，在 SQL API 中，可以对混合类型的属性进行范围查询。 例如，`grade` 可能是某些项中 `5` 之类的数字，如其他项中的 `grade4`。 在这些情况下，如同在 JavaScript 中一样，两种不同类型之间的比较导致 `Undefined`，因此跳过了该项。

> [!TIP]
> 为获得更快的查询执行时间，请创建索引策略，该策略对 BETWEEN 子句筛选器的任何数值属性或路径使用范围索引类型。

## <a name="distinct"></a>DISTINCT

DISTINCT 关键字可消除查询投影中的重复项。

在此示例中，查询将为每个姓氏输入值：

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

您还可以投影唯一对象。 在这种情况下，这两个文档中不存在 lastName 字段，因此查询返回空对象。

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

DISTINCT 还可以在子查询的投影中使用：

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

此查询投影一个数组，其中包含每个子级的 givenName，并删除重复项。 此数组的别名为 ChildNames，并在外部查询中投影。

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

不支持带有聚合系统函数和具有 DISTINCT 的子查询的查询。 例如，不支持以下查询：

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

使用 IN 关键字检查指定的值是否与列表中的任何值匹配。 例如，下面的查询将返回 `id` `WakefieldFamily` 或 `AndersenFamily`的所有家族项。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

下面的示例返回状态为任何指定值的所有项：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API 为[循环访问 JSON 数组](sql-query-object-array.md#Iteration)提供了支持，并通过 FROM source 中的 in 关键字添加了一个新构造。

如果在 `IN` 筛选器中包含分区键，则查询将自动仅筛选相关的分区。

## <a name="top"></a>返回页首

TOP 关键字返回未定义顺序的第一个 `N` 查询结果数。 最佳做法是将 TOP 与 ORDER BY 子句结合使用，将结果限制为有序值的第一个 `N`。 将这两个子句组合在一起是以可预测的方式指示顶级影响哪些行。

可以将 TOP 与常量值（如下面的示例所示）或使用参数化查询的变量值一起使用。

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