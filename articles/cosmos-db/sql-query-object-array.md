---
title: 在 Azure Cosmos DB 中使用数组和对象
description: 了解用于在 Azure Cosmos DB 中创建数组和对象的 SQL 语法。 本文还提供了一些示例，用于对数组对象执行操作
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386994"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用数组和对象

Azure Cosmos DB SQL API 的一项重要功能是数组和对象创建。

## <a name="arrays"></a>数组

可以构造数组，如下面的示例中所示：

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

结果有：

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

您还可以使用[数组表达式](sql-query-subquery.md#array-expression)从[子查询的](sql-query-subquery.md)结果中构造一个数组。 此查询获取数组中的子级的所有非重复的给定名称。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>迭代

SQL API 为循环访问 JSON 数组提供了支持，并通过 FROM source 中的[IN 关键字](sql-query-keywords.md#in)添加了一个新构造。 在以下示例中：

```sql
    SELECT *
    FROM Families.children
```

结果有：

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

下一个查询在 `Families` 容器中的 `children` 执行迭代。 输出数组与前面的查询不同。 此示例将 `children`拆分，并将结果平展到单个数组中：  

```sql
    SELECT *
    FROM c IN Families.children
```

结果有：

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

可以进一步筛选数组的每个单独条目，如以下示例中所示：

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

结果有：

```json
    [{
      "givenName": "Lisa"
    }]
```

您还可以对数组迭代的结果进行聚合。 例如，以下查询对所有家庭中的子节点数进行计数：

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

结果有：

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [联接](sql-query-join.md)