---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 了解 Azure Cosmos DB 的 SQL ORDER BY 子句。 使用 SQL 作为 Azure Cosmos DB JSON 查询语言。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188733"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 ORDER BY 子句

可选的 ORDER BY 子句指定查询返回的结果的排序顺序。

## <a name="syntax"></a>语法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>参数
  
- `<sort_specification>`  
  
   指定查询结合集要进行排序的属性或表达式。 可以将排序列指定为名称或属性别名。  
  
   可以指定多个属性。 属性名称必须是唯一的。 ORDER BY 子句中排序属性的顺序定义了排序结果集的组织。 即：结果集按第一个属性排序，然后该排序列表按第二个属性排序，依此类推。  
  
   在 ORDER BY 子句中引用的属性名称必须与 select 列表中的属性或 FROM 子句中指定的集合中定义的属性相对应，而不会有任何多义性。  
  
- `<sort_expression>`  
  
   指定对查询结果集进行排序时所依据的一个或多个属性或表达式。  
  
- `<scalar_expression>`  
  
   请参阅[标量表达式](sql-query-scalar-expressions.md)部分，了解详细信息。  
  
- `ASC | DESC`  
  
   指定按升序或降序排列指定列中的值。 ASC 按从最低值到最高值的顺序进行排序。 DESC 按从最高值到最低值的顺序进行排序。 ASC 是默认排序顺序。 Null 值被视为最低的可能值。  
  
## <a name="remarks"></a>备注  
  
   `ORDER BY` 子句要求索引策略包含要排序的字段的索引。 Azure Cosmos DB 查询运行时支持根据属性名称进行排序，而不支持对计算属性进行排序。 Azure Cosmos DB 支持多个 `ORDER BY` 属性。 若要运行包含多个排序依据属性的查询，您应该对要排序的字段定义一个[组合索引](index-policy.md#composite-indexes)。

> [!Note]
> 如果要排序的属性对于某些文档而言可能是未定义的，并且您想要在 ORDER BY 查询中检索这些属性，则必须在索引中显式包含此路径。 默认索引策略不允许检索未定义 sort 属性的文档。 [查看包含某些缺失字段的文档的示例查询](#documents-with-missing-fields)。

## <a name="examples"></a>示例

例如，以下查询将按居民姓名的升序检索家族：

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

下面的查询按项目创建日期的顺序检索系列 `id`。 Item `creationDate` 是表示*纪元时间*的数字，或自1970年1月1日起经过的时间（以秒为单位）。

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

此外，还可以按多个属性进行排序。 按多个属性排序的查询需要[组合索引](index-policy.md#composite-indexes)。 请考虑下列查询：

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

此查询按城市名称的升序检索系列 `id`。 如果有多个项目具有相同的城市名称，则查询将按 `creationDate` 按降序排序。

## <a name="documents-with-missing-fields"></a>缺失字段的文档

对于具有默认索引策略的容器运行 `ORDER BY` 的查询，不会返回未定义 sort 属性的文档。 如果要包括未定义 sort 属性的文档，则应在索引策略中显式包含此属性。

例如，下面是一个容器，其中包含一个索引策略，除了 `"/*"`之外，不会显式包含任何路径：

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

如果在 `Order By` 子句中运行包含 `lastName` 的查询，则结果将只包括定义了 `lastName` 属性的文档。 我们尚未为 `lastName` 定义显式包含的路径，因此查询结果中不会出现任何没有 `lastName` 的文档。

下面是对两个文档 `lastName` 排序的查询，其中一个文档没有定义 `lastName`：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

结果仅包括具有定义的 `lastName`的文档：

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

如果我们将容器的索引策略更新为显式包含 `lastName`的路径，则在查询结果中将包含带有未定义的排序属性的文档。 必须显式定义路径，使其导致此标量值（而不是超出它的值）。 你应在索引策略的路径定义中使用 `?` 字符，以确保将 `lastName` 属性显式编入索引，而不会在其之外附加嵌套路径。

下面是一个示例索引策略，允许你在查询结果中显示带有未定义 `lastName` 的文档：

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

如果再次运行相同的查询，则在查询结果中首先出现 `lastName` 缺少的文档：

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

如果将排序顺序修改为 `DESC`，则缺少 `lastName` 的文档将显示在查询结果中的最后：

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

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB 中的索引编制策略](index-policy.md)
- [OFFSET LIMIT 子句](sql-query-offset-limit.md)
