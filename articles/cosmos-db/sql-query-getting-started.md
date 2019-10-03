---
title: Azure Cosmos DB 中的 SQL 查询入门
description: SQL 查询简介
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 8de5140d0146ccbb18f41867e1c716aa2f3897b7
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001910"
---
# <a name="getting-started-with-sql-queries"></a>SQL 查询入门

Azure Cosmos DB SQL API 帐户支持使用 结构化查询语言 (SQL) 作为 JSON 查询语言来查询项。 Azure Cosmos DB 查询语言的设计目标是：

* 支持用户最熟悉的、最流行的 SQL 查询语言，而不是要发明一种新的查询语言。 SQL 提供正式的编程模型用于对 JSON 项进行丰富查询。  

* 使用 JavaScript 的编程模型作为查询语言的基础。 JavaScript 的类型系统、表达式计算和函数调用是 SQL API 的根。 这些根为关系投影、跨 JSON 项的分层导航、自联接、空间查询以及调用完全采用 JavaScript 编写的用户定义的函数 (UDF) 等功能提供自然编程模型。

## <a name="upload-sample-data"></a>上传示例数据

在 Cosmos DB SQL API 帐户中，创建名为 `Families` 的容器。 在容器中创建两个简单的 JSON 项。 可以使用此数据集运行 Azure Cosmos DB 查询文档中的大多数示例查询。

### <a name="create-json-items"></a>创建 JSON 项

以下代码创建两个有关家庭的简单 JSON 项。 Andersen 和 Wakefield 家庭的简单 JSON 项包括父母、孩子及其宠物、地址和注册信息。 第一个项包含字符串、数字、布尔、数组和嵌套属性。


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

第二个项使用 `givenName` 和 `familyName`，而不是使用 `firstName` 和 `lastName`。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>查询 JSON 项

尝试对此 JSON 数据执行一些查询来了解 Azure Cosmos DB 的 SQL 查询语言的一些重要方面。

以下查询返回其中的 `id` 字段与 `AndersenFamily` 匹配的项。 由于它是一个 `SELECT *` 查询，因此该查询的输出是完整的 JSON 项。 有关 SELECT 语法的详细信息，请参阅 [SELECT 语句](sql-query-select.md)。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查询结果为： 

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

以下查询将 JSON 输出的格式重新设置为不同的形式。 当地址中的城市名称与州名称相同时，该查询将使用两个选定的字段 `Name` 和 `City` 来投影新的 JSON `Family` 对象。 “NY, NY”符合这种情况。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查询结果为：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

以下查询返回家庭中 `id` 匹配 `WakefieldFamily` 的所有孩子的名字，按城市排序。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

其结果是：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>备注

上述示例演示了 Cosmos DB 查询语言的几个方面：  

* 由于 SQL API 适用于 JSON 值，因此它可以处理三种形式的实体，而不是行和列。 可以引用任意深度的树节点（例如 `Node1.Node2.Node3…..Nodem`），类似于 ANSI SQL 中的 `<table>.<column>` 的两部分引用。

* 由于查询语言适用于无架构数据，因此，必须动态绑定类型系统。 相同的表达式在不同项上可能会产生不同的类型。 查询的结果是有效的 JSON 值，但不保证它是固定的架构。  

* Azure Cosmos DB 仅支持严格的 JSON 项。 类型系统和表达式仅限于处理 JSON 类型。 有关详细信息，请参阅 [JSON 规范](https://www.json.org/)。  

* Cosmos 容器是 JSON 项的一个无架构集合。 容器项内部以及跨容器项的关系是按包含关系隐式捕获的，而不是按主键和外键关系捕获的。 此特性对于本文稍后要讨论的项内联接非常重要。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
