---
title: Azure Cosmos DB 中的用户定义函数 (Udf)
description: 了解有关 Azure Cosmos DB 中的用户定义函数。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342773"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB 中的用户定义函数 (Udf)

SQL API 支持用户定义函数 (UDF)。 使用标量 UDF，可以传入零个或多个参数，并返回单个参数结果。 API 会检查每个参数 JSON 值是否合法。  

API 扩展了 SQL 语法，支持使用 UDF 的自定义应用程序逻辑。 可将 UDF 注册到 SQL API，然后在 SQL 查询中引用它们。 事实上，UDF 经过精心设计，可从查询调用。 作为一种定理，UDF 不能像其他 JavaScript 类型（例如存储过程和触发器）一样访问上下文对象。 查询是只读的，可以在主要或次要副本上运行。 与其他 JavaScript 类型不同，UDF 只能在次要副本上运行。

以下示例在 Cosmos DB 数据库中的某个项容器下注册一个 UDF。 该示例创建了名为 `REGEX_MATCH` 的 UDF。 它接受两个 JSON 字符串值：`input` 和 `pattern`，并使用 JavaScript 的 `string.match()` 函数检查第一个值是否与第二个值中指定的模式相匹配。

## <a name="examples"></a>示例

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

其结果是：

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

其结果是：

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

其结果是：

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

- [Azure Cosmos DB 简介](introduction.md)
- [系统函数](sql-query-system-functions.md)
- [聚合](sql-query-aggregates.md)