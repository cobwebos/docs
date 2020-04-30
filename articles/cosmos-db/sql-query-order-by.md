---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 了解 Azure Cosmos DB 的 SQL ORDER BY 子句。 将 SQL 用作 Azure Cosmos DB JSON 查询语言。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641400"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 ORDER BY 子句

Optional `ORDER BY`子句指定查询返回的结果的排序顺序。

## <a name="syntax"></a>语法
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>参数
  
- `<sort_specification>`  
  
   指定查询结合集要进行排序的属性或表达式。 可将排序列指定为名称或属性别名。  
  
   可以指定多个属性。 属性名称必须唯一。 `ORDER BY`子句中排序属性的顺序定义了排序结果集的组织。 即：结果集按第一个属性排序，然后该排序列表按第二个属性排序，依此类推。  
  
   `ORDER BY`子句中引用的属性名称必须与 select 列表中的属性或在`FROM`子句中指定的集合中定义的属性相对应。  
  
- `<sort_expression>`  
  
   指定一个或多个属性或表达式用作排序查询结果集的依据。  
  
- `<scalar_expression>`  
  
   请参阅[标量表达式](sql-query-scalar-expressions.md)部分，了解详细信息。  
  
- `ASC | DESC`  
  
   指定按升序或降序排列指定列中的值。 `ASC`从最低值到最高值排序。 `DESC`从最高值到最低值排序。 `ASC`是默认的排序顺序。 Null 值被视为最低的可能值。  
  
## <a name="remarks"></a>备注  
  
   `ORDER BY`子句要求索引策略包含要排序的字段的索引。 Azure Cosmos DB 查询运行时支持根据属性名称排序，而不支持根据计算的属性排序。 Azure Cosmos DB 支持多`ORDER BY`个属性。 若要运行包含多个 ORDER BY 属性的查询，应在所要排序的字段中定义[组合索引](index-policy.md#composite-indexes)。

> [!Note]
> 如果要排序的属性对于某些文档而言可能是未定义的，并且您想要在 ORDER BY 查询中检索这些属性，则必须在索引中显式包含此路径。 默认索引策略不允许检索未定义排序属性的文档。 [查看包含某些缺失字段的文档的示例查询](#documents-with-missing-fields)。

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

以下查询按项的创建日期检索家庭 `id`。 项 `creationDate` 是一个数字，表示纪元时间，或者自 1970 年 1 月 1 日开始消逝的时间（以秒为单位）。**

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

## <a name="documents-with-missing-fields"></a>缺失字段的文档

针对具有`ORDER BY`默认索引策略的容器运行的查询不会返回未定义 sort 属性的文档。 如果要包括未定义 sort 属性的文档，则应在索引策略中显式包含此属性。

例如，下面是一个容器，其中包含除了之外`"/*"`的任何路径的索引策略：

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

如果运行包含`lastName`在`Order By`子句中的查询，则结果将只包括定义了`lastName`属性的文档。 我们尚未定义显式包含的`lastName`路径，因此，任何没有的`lastName`文档都不会出现在查询结果中。

下面的查询按`lastName`两个文档排序，其中一个文档没有`lastName`定义：

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

结果仅包括定义`lastName`了的文档：

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

如果我们将容器的索引策略更新为显式包含的路径`lastName`，则在查询结果中将包含带有未定义的排序属性的文档。 必须显式定义路径，使其导致此标量值（而不是超出它的值）。 你应在索引`?`策略的路径定义中使用字符，以确保显式索引属性`lastName` ，但不会在它的外部附加嵌套路径。 如果`Order By`查询使用[复合索引](index-policy.md#composite-indexes)，则结果将始终在查询结果中包含带有未定义的排序属性的文档。

下面是一个示例索引策略，该策略允许你将包含未定义`lastName`的文档显示在查询结果中：

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

如果再次运行相同的查询，则在查询结果中`lastName`首先会出现丢失的文档：

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

如果将排序顺序更改为`DESC`，则在查询结果中`lastName`最后出现的文档将显示为最后：

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
