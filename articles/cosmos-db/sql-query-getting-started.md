---
title: Azure Cosmos DB 中的 SQL 查询入门
description: 了解如何使用 SQL 查询从 Azure Cosmos DB 查询数据。 可以 Azure Cosmos DB 中将示例数据上传到容器，并对其进行查询。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873329"
---
# <a name="getting-started-with-sql-queries"></a>SQL 查询入门

Azure Cosmos DB SQL API 帐户支持使用结构化查询语言（SQL）查询项作为 JSON 查询语言。 Azure Cosmos DB 查询语言的设计目标是：

* 支持 SQL，这是最常见的一种查询语言，而不是发明一种新的查询语言。 SQL 提供一种正式的编程模型，用于对 JSON 项进行丰富的查询。  

* 使用 JavaScript 的编程模型作为查询语言的基础。 JavaScript 的类型系统、表达式计算和函数调用是 SQL API 的根。 这些根为关系投影、跨 JSON 项的分层导航、自联接、空间查询以及完全用 JavaScript 编写的用户定义函数（Udf）的调用提供了一个自然的编程模型。

## <a name="upload-sample-data"></a>上传示例数据

在 SQL API Cosmos DB 帐户中，创建一个名为 `Families`的容器。 在容器中创建两个简单的 JSON 项。 您可以使用此数据集在 Azure Cosmos DB 查询文档中运行大部分示例查询。

### <a name="create-json-items"></a>创建 JSON 项

下面的代码创建两个有关系列的简单 JSON 项。 Andersen 和 Wakefield 系列的简单 JSON 项包括家长、孩子及其宠物、地址和注册信息。 第一项包含字符串、数字、布尔值、数组和嵌套属性。


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

第二项使用 `givenName` 和 `familyName`，而不是 `firstName` 和 `lastName`。

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

尝试对 JSON 数据进行一些查询，以了解 Azure Cosmos DB 的 SQL 查询语言的一些关键方面。

下面的查询将返回 `id` 字段匹配 `AndersenFamily`的项。 由于它是 `SELECT *` 查询，因此查询的输出为完整的 JSON 项。 有关 SELECT 语法的详细信息，请参阅[select 语句](sql-query-select.md)。 

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

下面的查询将 JSON 输出重新格式化为不同的形状。 当地址城市与州相同时，查询将使用两个选定的字段（`Name` 和 `City`）来投影一个新的 JSON `Family` 对象。 "NY，NY" 匹配这种情况。

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

下面的查询将返回系列中子女的所有名字，其 `id` 匹配 `WakefieldFamily`，按 city 排序。

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

前面的示例显示了 Cosmos DB 查询语言的几个方面：  

* 由于 SQL API 适用于 JSON 值，因此它处理的是树形状的实体，而不是行和列。 您可以按任意深度（如 `Node1.Node2.Node3…..Nodem`）引用树节点，这类似于 ANSI SQL 中 `<table>.<column>` 的两部分引用。

* 由于查询语言适用于无架构数据，因此必须动态绑定类型系统。 相同的表达式在不同项上可能会产生不同的类型。 查询的结果是一个有效的 JSON 值，但不一定是固定的架构。  

* Azure Cosmos DB 仅支持严格的 JSON 项。 类型系统和表达式仅限于处理 JSON 类型。 有关详细信息，请参阅[JSON 规范](https://www.json.org/)。  

* Cosmos 容器是 JSON 项的无架构集合。 容器项内和跨容器项的关系通过包含（而不是按主键和外键关系）隐式捕获。 此功能对于本文后面讨论的项目内联接非常重要。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
