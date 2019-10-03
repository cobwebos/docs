---
title: FROM 子句中 Azure Cosmos DB
description: 了解 Azure Cosmos DB SQL FROM 子句
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 6bc93569dc9a0405ec3a8dfd719c89ede01df84d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342504"
---
# <a name="from-clause"></a>FROM 子句

FROM (`FROM <from_specification>`) 子句是可选的，除非稍后在查询中对源进行筛选或投影。 `SELECT * FROM Families` 之类的查询枚举整个 `Families` 容器。 还可以对容器使用特殊标识符 ROOT，而无需使用容器名称。

FROM 子句对每个查询强制实施以下规则：

* 容器可以使用别名，如 `SELECT f.id FROM Families AS f` 或只需为 `SELECT f.id FROM Families f`。 此处的 `f` 是 `Families` 的别名。 如为可选的关键字[别名](sql-query-aliasing.md)标识符。  

* 指定别名后，无法绑定原始的源名称。 例如，`SELECT Families.id FROM Families f` 在语法上是无效的，原因是标识符 `Families` 已指定别名，因此不再可以解析。  

* 所有被引用的属性必须完全限定，以避免在不严格遵守架构时出现任何有歧义的绑定。 例如，`SELECT id FROM Families f` 在语法上是无效的，因为未绑定属性 `id`。

## <a name="syntax"></a>语法
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>参数
  
- `<from_source>`  
  
  指定一个具有或不具有别名的数据源。 如果未指定别名，将从 `<container_expression>` 中使用以下规则推断：  
  
  -  如果表达式为 container_name，那么 container_name 将用作别名。  
  
  -  如果表达式为 `<container_expression>`，其次是 property_name，那么 property_name 将用作别名。 如果表达式为 container_name，那么 container_name 将用作别名。  
  
- AS `input_alias`  
  
  指定 `input_alias` 为基础容器表达式返回的一组值。  
 
- `input_alias` IN  
  
  指定 `input_alias` 应表示通过遍历由基础容器表达式返回的每个数组的所有数组元素获得的一组值。 基础容器表达式返回的非数组的任何值都将被忽略。  
  
- `<container_expression>`  
  
  指定要用于检索文档的容器表达式。  
  
- `ROOT`  
  
  指定应从当前连接的默认容器中检索文档。  
  
- `container_name`  
  
  指定应从当前提供的容器中检索文档。 容器的名称必须匹配当前连接到的容器名称。  
  
- `input_alias`  
  
  指定应从提供的别名定义的其他源中检索文档。  
  
- `<container_expression> '.' property_`  
  
  指定应通过访问由指定容器表达式检索的所有文档的 `property_name` 属性或 array_index 数组元素来检索文档。  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  指定应通过访问由指定容器表达式检索的所有文档的 `property_name` 属性或 array_index 数组元素来检索文档。  
  
## <a name="remarks"></a>备注
  
`<from_source>(`) 中提供或推断的所有别名必须唯一。 语法 `<container_expression>.`property_name 等同于 `<container_expression>' ['"property_name"']'`。 但是，如果属性名称包含非标识符字符，则可以使用后一种语法。  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>处理未命中属性、未命中数组元素和未定义值
  
如果某个容器表达式访问属性或数组元素，但该值不存在，将忽略该值，且不会做进一步处理。  
  
### <a name="container-expression-context-scoping"></a>容器表达式上下文范围  
  
容器表达式的范围可以是集合或文档：  
  
-   如果容器表达式的基础源是 ROOT 或 `container_name`，则表达式的范围是容器。 此类表达式表示从容器中直接检索的一组文档，且不依赖于其他容器表达式的处理。  
  
-   如果容器表达式的基础源是之前在查询中引入的 `input_alias`，则表达式的范围是文档。 此类表达式表示通过计算每个文档范围内的容器表达式获得的一组文档，该文档属于与别名容器相关联的集。  结果集为多个集的合并，通过计算基础集中每个文档的容器表达式获得。 

## <a name="examples"></a>示例

### <a name="get-subitems-by-using-the-from-clause"></a>使用 FROM 子句获取子项

FROM 子句可将源化简为更小的子集。 要在每个项中仅枚举子树，子根可能会变成源，如以下示例所示：

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
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
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

上述查询使用数组作为源，但你也可以使用对象作为源。 该查询考虑将源中任何有效的已定义 JSON 值包含在结果中。 以下示例将排除`Families`没有`address.state`值。

```sql
    SELECT *
    FROM Families.address.state
```

其结果是：

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>后续步骤

- [入门](sql-query-getting-started.md)
- [SELECT 子句](sql-query-select.md)
- [WHERE 子句](sql-query-where.md)