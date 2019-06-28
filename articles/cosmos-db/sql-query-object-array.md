---
title: 处理数组和 Azure Cosmos DB 中的对象
description: 了解有关数组和对象创建的 SQL 语法适用于 Azure Cosmos DB。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342707"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>处理数组和 Azure Cosmos DB 中的对象

Azure Cosmos DB SQL API 的一个重要功能是数组和对象创建。

## <a name="arrays"></a>数组

您可以构造数组，如下面的示例中所示：

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

其结果是：

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

此外可以使用[数组表达式](sql-query-subquery.md#array-expression)构造从数组[子查询的](sql-query-subquery.md)结果。 此查询，获取一个数组中的子对象的所有非重复给定名称。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>迭代

SQL API 提供支持，以为遍历 JSON 数组，与通过添加新的构造[IN 关键字](sql-query-keywords.md#in)FROM 源中。 在以下示例中：

```sql
    SELECT *
    FROM Families.children
```

其结果是：

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

下一个查询循环访问 `Families` 容器中的 `children`。 输出的数组与前面的查询不同。 此示例拆分 `children` 并将结果平展为单个数组：  

```sql
    SELECT *
    FROM c IN Families.children
```

其结果是：

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

可以进一步筛选该数组的每个条目，如以下示例所示：

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

其结果是：

```json
    [{
      "givenName": "Lisa"
    }]
```

还可基于数组迭代的结果进行聚合。 例如，以下查询计数所有家庭中的孩子数目。

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

其结果是：

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [联接](sql-query-join.md)