---
title: Azure Cosmos DB 中的 SELECT 子句
description: 了解 Azure Cosmos DB 的 SQL SELECT 子句。 使用 SQL 作为 Azure Cosmos DB JSON 查询语言。
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: b90fc6f1f50ec2ea75619188cca36f78061f28df
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326789"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 SELECT 子句

每个查询都由 SELECT 子句和可选的[from](sql-query-from.md)和[WHERE](sql-query-where.md)子句（每个 ANSI SQL 标准）组成。 通常，枚举 FROM 子句中的源，WHERE 子句对源应用筛选器以检索 JSON 项的子集。 然后，SELECT 子句会在选择列表中投影请求的 JSON 值。

## <a name="syntax"></a>语法

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>参数
  
- `<select_specification>`  

  为结果集选择的属性或值。  
  
- `'*'`  

  指定在不进行任何更改的情况下检索该值。 具体来说，如果处理的值是一个对象，将检索所有属性。  
  
- `<object_property_list>`  
  
  指定要检索的属性列表。 每个返回值将是一个具有指定属性的对象。  
  
- `VALUE`  

  指定应检索 JSON 值而不是完整的 JSON 对象。 与 `<property_list>` 不同的是，它不会将投影值包装在对象中。  
 
- `DISTINCT`
  
  指定应删除投影属性的重复项。  

- `<scalar_expression>`  

  表示要计算的值的表达式。 请参阅[标量表达式](sql-query-scalar-expressions.md)部分，了解详细信息。  

## <a name="remarks"></a>备注

只有当 FROM 子句已声明一个别名时，`SELECT *` 语法才有效。 `SELECT *` 提供一个标识投影，在不需要投影的情况下会有所帮助。 只有当指定了 FROM 子句，并且该字句只引入单个输入源时，SELECT * 才有效。  
  
`SELECT <select_list>` 和 `SELECT *` 是“语法糖”，可另外使用简单的 SELECT 语句表示，如下所示。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   等效于：  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   等效于：  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>示例

以下 SELECT 查询示例从 `Families` 中返回 `address`，其中 `id` 匹配 `AndersenFamily`：

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>带引号的属性访问器
您可以使用带引号的属性运算符 [] 访问属性。 例如，`SELECT c.grade` 和 `SELECT c["grade"]` 是等效的。 此语法可用于对包含空格、特殊字符或与 SQL 关键字或保留字同名的属性进行转义。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>嵌套属性

下面的示例投影两个嵌套的属性 `f.address.state` 和 `f.address.city`。

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>JSON 表达式

投影也支持 JSON 表达式，如下面的示例中所示：

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

在前面的示例中，SELECT 子句需要创建 JSON 对象，并且由于该示例未提供任何键，因此子句使用隐式参数变量名 `$1`。 下面的查询返回两个隐式参数变量： `$1` 和 `$2`。

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 子句](sql-query-where.md)