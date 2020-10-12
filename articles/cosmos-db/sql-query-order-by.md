---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 了解 Azure Cosmos DB 的 SQL ORDER BY 子句。 将 SQL 用作 Azure Cosmos DB JSON 查询语言。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: c4ae66884602989284a427bdc33de7612bd9a8df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84484327"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 ORDER BY 子句

可选的 `ORDER BY` 子句指定查询返回的结果的排序顺序。

## <a name="syntax"></a>语法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>参数
  
- `<sort_specification>`  
  
   指定对查询结果集进行排序时要依据的属性或表达式。 可将排序列指定为名称或属性别名。  
  
   可以指定多个属性。 属性名称必须唯一。 `ORDER BY` 子句中排序属性的顺序定义了排序的结果集的组织方式。 也就是说，结果集首先按第一个属性排序，然后该有序列表按第二个属性排序，依此类推。  
  
   `ORDER BY` 子句中引用的属性名称必须与所选列表中的某个属性或者与 `FROM` 子句中指定的集合中定义的某个属性相对应，且不存在任何多义性。  
  
- `<sort_expression>`  
  
   指定一个或多个属性或表达式用作排序查询结果集的依据。  
  
- `<scalar_expression>`  
  
   有关详细信息，请参阅[标量表达式](sql-query-scalar-expressions.md)部分。  
  
- `ASC | DESC`  
  
   指定应当按升序或降序对指定列中的值进行排序。 `ASC` 将按照从最低值到最高值的顺序排序。 `DESC` 将按照从最高值到最低值的顺序排序。 `ASC` 是默认排序顺序。 Null 值被视为最低的可能值。  
  
## <a name="remarks"></a>备注  
  
   `ORDER BY` 子句要求索引策略包含所要排序的字段的索引。 Azure Cosmos DB 查询运行时支持根据属性名称排序，而不支持根据计算的属性排序。 Azure Cosmos DB 支持多个 `ORDER BY` 属性。 若要运行包含多个 ORDER BY 属性的查询，应在所要排序的字段中定义[组合索引](index-policy.md#composite-indexes)。

> [!Note]
> 如果要排序的属性对于某些文档而言可能未定义，并且你希望在 ORDER BY 查询中检索这些属性，则必须在索引中显式包含此路径。 默认索引策略不允许检索未定义排序属性的文档。 [查看针对缺少一些字段的文档的示例查询](#documents-with-missing-fields)。

## <a name="examples"></a>示例

例如，以下查询按居住城市名称的升序检索家庭：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

结果有：

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

以下查询按项的创建日期检索家庭 `id`。 项 `creationDate` 是一个数字，表示纪元时间，或者自 1970 年 1 月 1 日开始消逝的时间（以秒为单位）。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

结果有：

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

此外，可按多个属性排序。 按多个属性排序的查询需要[组合索引](index-policy.md#composite-indexes)。 请考虑下列查询：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查询按城市名称的升序检索家庭 `id`。 如果多个项包含同一个城市名称，该查询将按 `creationDate` 的降序排序。

## <a name="documents-with-missing-fields"></a>缺少字段的文档

针对采用默认索引策略的容器运行包含 `ORDER BY` 的查询不会返回未定义排序属性的文档。 若要包含未定义排序属性的文档，应在索引策略中显式包含此属性。

例如，以下容器的索引策略未显式包含除 `"/*"` 以外的其他任何路径：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

如果运行一个在 `Order By` 子句中包含 `lastName` 的查询，则结果只包括定义了 `lastName` 属性的文档。 我们尚未为 `lastName` 定义显式包含路径，因此查询结果中不会显示任何没有 `lastName` 的文档。

以下查询按 `lastName` 对两个文档进行排序，对于其中的一个文档，尚未定义 `lastName`：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

结果仅包括定义了 `lastName` 的文档：

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

如果我们将该容器的索引策略更新为显式包含 `lastName` 的路径，则查询结果中会包括具有未定义排序属性的文档。 必须显式定义路径才能生成此标量值（而不是生成其他值）。 应在索引策略的路径定义中使用 `?` 字符，确保为 `lastName` 属性显式编制索引，且不会包含除此之外的其他嵌套路径。 如果 `Order By` 查询使用[组合索引](index-policy.md#composite-indexes)，则查询结果中始终包括具有未定义排序属性的文档。

使用以下示例索引策略可以在查询结果中显示具有未定义的 `lastName` 的文档：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

如果再次运行同一查询，则缺少 `lastName` 的文档会显示在查询结果中的最前面：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

结果有：

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

如果将排序顺序修改为 `DESC`，则缺少 `lastName` 的文档会显示在查询结果中的最后面：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

结果有：

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> 仅 .NET SDK 3.4.0 或更高版本支持混合类型的 ORDER BY。 因此，如果要按未定义值和已定义值的组合进行排序，则应使用此版本（或更高版本）。

你无法控制不同类型在结果中的显示顺序。 在上面的示例中，我们演示了如何在字符串值之前对未定义值进行排序。 例如，如果想更好地控制未定义值的排序顺序，则可为任何未定义属性分配字符串值“aaaaaaaaa”或“zzzzzzzz”，以确保它们是第一个或最后一个。

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB 中的索引编制策略](index-policy.md)
- [OFFSET LIMIT 子句](sql-query-offset-limit.md)
