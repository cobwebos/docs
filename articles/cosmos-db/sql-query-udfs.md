---
title: Azure Cosmos DB 中的用户定义函数 (UDF)
description: 了解 Azure Cosmos DB 中的用户定义函数。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011117"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB 中的用户定义函数 (UDF)

SQL API 支持用户定义函数 (UDF)。 使用标量 UDF，可以传入零个或多个参数，并返回单个参数结果。 API 会检查每个参数 JSON 值是否合法。  

## <a name="udf-use-cases"></a>UDF 用例

API 扩展了 SQL 语法，支持使用 UDF 的自定义应用程序逻辑。 可将 UDF 注册到 SQL API，然后在 SQL 查询中引用它们。 与存储过程和触发器不同，UDF 是只读的。

使用 UF，可以扩展 Azure Cosmos DB 的查询语言。 UF 是在查询投影中表达复杂业务逻辑的好方法。

但是，我们建议在：

- Azure Cosmos DB 中已存在等效[的系统函数](sql-query-system-functions.md)。 系统功能将始终使用比等效 UDF 更少的 RU。
- UDF 是查询子句中`WHERE`的唯一筛选器。 UDF 不使用索引，因此评估 UDF 需要加载文档。 将使用索引的其他筛选器谓词与 UDF 结合在`WHERE`子句中，将减少 UDF 处理的文档数。

如果在查询中必须多次使用相同的 UDF，则应在[子查询](sql-query-subquery.md#evaluate-once-and-reference-many-times)中引用 UDF，从而允许您使用 JOIN 表达式计算 UDF 一次，但多次引用它。

## <a name="examples"></a>示例

以下示例在 Cosmos 数据库中的某个项容器下注册一个 UDF。 该示例创建了名为 `REGEX_MATCH` 的 UDF。 它接受两个 JSON 字符串值：`input` 和 `pattern`，并使用 JavaScript 的 `string.match()` 函数检查第一个值是否与第二个值中指定的模式相匹配。

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

现在，请在查询投影中使用此 UDF。 从查询内部调用 UDF 时，必须使用区分大小写的前缀 `udf.` 来限定 UDF。

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

结果有：

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

可以在筛选器中使用以 `udf.` 前缀限定的 UDF，如以下示例所示：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

结果有：

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

从本质上来说，UDF 是可以在投影和筛选器中使用的有效标量表达式。

为了进一步了解 UDF 的强大功能，让我们查看使用条件逻辑的另一个示例：

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

以下示例运用了 UDF：

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

结果有：

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

如果 UDF 参数引用的属性在 JSON 值中未提供，则会将该参数视为未定义，因此会跳过 UDF 调用。 同样，如果未定义 UDF 的结果，则不会将此 UDF 包含在结果中。

如以上示例所示，UDF 将 JavaScript 语言的强大功能与 SQL API 相集成。 UDF 提供丰富的可编程接口来执行复杂的过程，并借助内置的 JavaScript 运行时功能来执行条件逻辑。 SQL API 在当前的 WHERE 或 SELECT 子句处理阶段，为每个源项的 UDF 提供参数。 结果将无缝整合到总体执行管道中。 总而言之，UDF 是在查询过程中执行复杂业务逻辑的极佳工具。

## <a name="next-steps"></a>后续步骤

- [Azure 宇宙 DB 简介](introduction.md)
- [系统函数](sql-query-system-functions.md)
- [聚合](sql-query-aggregates.md)