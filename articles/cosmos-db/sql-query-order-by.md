---
title: Azure Cosmos DB 中的 ORDER BY 子句
description: 了解 Azure Cosmos DB 的 SQL ORDER BY 子句。 使用 SQL 作为 Azure Cosmos DB JSON 查询语言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 5cae2bdd7d1f2f26e626c81ea95d2cee3cc8ae13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444792"
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
  
   ORDER BY 子句要求索引策略包含要排序的字段的索引。 Azure Cosmos DB 查询运行时支持根据属性名称进行排序，而不支持对计算属性进行排序。 Azure Cosmos DB 支持多个排序依据属性。 若要运行包含多个排序依据属性的查询，您应该对要排序的字段定义一个[组合索引](index-policy.md#composite-indexes)。
   
> [!Note] 
> 使用 .NET SDK 3.4.0 或更高版本时，如果对某些文档而言，要进行排序的属性可能是不确定的，则需要对这些属性显式创建索引。 默认索引策略将不允许检索未定义 sort 属性的文档。

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

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [OFFSET LIMIT 子句](sql-query-offset-limit.md)
