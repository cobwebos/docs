---
title: 适用于 Azure Cosmos DB SQL 子查询
description: 了解有关 SQL 子查询和 Azure Cosmos DB 中的其常见用例
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 48d0c7a022ff568582637aac36a377ca022a413c
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977358"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>用于 Azure Cosmos DB SQL 子查询示例

子查询是一个嵌套在另一个查询。 内部查询或内部选择，也称为子查询。 包含一个子查询的语句通常称为外部查询。

本主题介绍 SQL 子查询和 Azure Cosmos DB 中的其常见用例。

## <a name="types-of-subqueries"></a>类型的子查询

有两种主要类型的子查询：

* **相关**:外部查询中引用的值的子查询。 子查询为外部查询处理的每一行计算一次。
* **非相关**:独立于外部查询子查询。 它可以在其自身而不依赖于外部查询上运行。

> [!NOTE]
> Azure Cosmos DB 支持仅相关子查询。

子查询可以根据它们返回的行和列的数量进行进一步分类。 有三种类型：
* **Table**：返回多个行和多个列。
* **多值**:返回多个行和单列。
* **标量**:返回单个行和单列。

Azure Cosmos DB 中的 SQL 查询始终返回单个列 （一个简单的值或复杂的文档）。 因此，仅多值和标量的子查询是适用于 Azure Cosmos DB。 作为关系表达式，可以仅在 FROM 子句中使用多值子查询。 作为在 SELECT 中的标量表达式或 WHERE 子句或 FROM 子句中对关系表达式，可以使用标量子查询。


## <a name="multi-value-subqueries"></a>多值子查询

多值子查询返回一组文档，并且始终使用的 FROM 子句中。 它们的用途：

* 优化联接表达式。 
* 一次计算昂贵的表达式和引用多次。

### <a name="optimize-join-expressions"></a>优化联接表达式

多值子查询可以通过将谓词推送后每个选择多表达式而不在 WHERE 子句中的所有交叉联接后优化联接表达式。

请考虑下列查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

对于此查询，索引将匹配任何文档的一个标记具有名称"婴幼儿公式。" 它是介于 0 和 10 之间的值与 nutrient 项和金额大于 1 的服务项。 应用任何筛选器之前，联接表达式此处将为每个匹配的文档执行叉积的标记、 nutrients 和 servings 数组的所有项。 

WHERE 子句将筛选器谓词的每个 < c、 t、 n、 s > 元组。 例如，如果匹配的文档中的三个数组的每个有 10 个项，它将扩展到 1 x 10 x 10 x 10 (即，为 1000) 的元组。 此处使用子查询可以帮助中加入与下一个表达式之前筛选掉联接的数组项。

此查询等效于前一次，但使用子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假定标记数组中的只有一项匹配筛选器，并且没有 nutrients 和 servings 数组的五个项。 联接表达式然后将扩展到 1 x 1 x 5 x 5 = 25 项，而不是第一个查询中包含 1000 项。

### <a name="evaluate-once-and-reference-many-times"></a>评估一次，并且引用很多时候

子查询可帮助优化查询与昂贵的表达式，如用户定义函数 (Udf)、 复杂的字符串或算术表达式。 联接表达式以及子查询可用于评估一次的表达式，但对其进行引用多次。

下面的查询运行 UDF`GetMaxNutritionValue`两次：

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

下面是 UDF 只运行一次的等效查询：

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 请记住联接表达式的叉积行为。 如果 UDF 表达式可以计算结果为未定义，则应确保联接表达式始终通过直接从子查询而不是值返回一个对象生成的单个行。
>

下面是返回一个对象而不是非值一个类似示例：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

方法不局限于 Udf。 它适用于任何可能会较昂贵的表达式。 例如，可以采用相同的方法使用数学函数`avg`:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>模拟与外部引用数据联接

通常，可能需要引用很少更改，例如单位的度量值或国家/地区代码的静态数据。 它是更好的做法不重复的每个文档的此类数据。 避免这种重复将节省的存储，并通过保持较小的文档大小来提高写入性能。 可以使用子查询来模拟 inner 联接语义与引用数据的集合。

例如，请考虑此组的引用数据：

| **单位** | **名称**            | **乘数** | **基本单位** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | 元语法          |
| µg       | Microgram           | 1.00E-06       | 元语法          |
| mg       | 毫克           | 1.00E-03       | 元语法          |
| G        | 元语法                | 1.00E+00       | 元语法          |
| kg       | Kilogram            | 1.00E+03       | 元语法          |
| mg       | Megagram            | 1.00E+06       | 元语法          |
| Gg       | Gigagram            | 1.00E+09       | 元语法          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| kJ       | Kilojoule           | 1.00E+03       | Joule         |
| MJ       | Megajoule           | 1.00E+06       | Joule         |
| GJ       | Gigajoule           | 1.00E+09       | Joule         |
| cal      | 热量             | 1.00E+00       | 热量       |
| kcal     | 热量             | 1.00E+03       | 热量       |
| IU       | 国际单位 |                |               |


下面的查询模仿联接此数据，以便向输出添加的单位的名称：

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>标量子查询

标量子查询表达式是计算结果为单个值的子查询。 标量子查询表达式的值是子查询投影 （SELECT 子句） 的值。  您可以使用标量子查询表达式中的标量表达式是有效的多个位置。 例如，可以使用任何表达式中这两个 SELECT 和 WHERE 子句中的一个标量子查询。

使用标量子查询始终不会有助于优化，不过。 例如，将标量子查询作为参数传递给系统或用户定义的函数提供了资源单位 (RU) 的消耗或延迟不会带来好处。

标量子查询可以进一步分类为：
* 简单表达式标量子查询
* 聚合标量子查询

### <a name="simple-expression-scalar-subqueries"></a>简单表达式标量子查询

简单表达式标量子查询为具有不包含任何聚合表达式的 SELECT 子句的相关子查询。 这些子查询提供任何优化好处，因为编译器将它们转换为一个较大的简单表达式。 内部和外部查询之间没有关联的上下文。

以下是几个示例：

**示例 1**

```sql
SELECT 1 AS a, 2 AS b
```

可以使用简单表达式标量子查询，重写此查询：

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

这两个查询生成以下输出：

```json
[
  { "a": 1, "b": 2 }
]
```

**示例 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

可以使用简单表达式标量子查询，重写此查询：

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

查询输出：

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**示例 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

可以使用简单表达式标量子查询，重写此查询：

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

查询输出：

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>聚合标量子查询

聚合标量子查询是具有其投影或计算结果为单个值的筛选器中使用聚合函数的子查询。

**示例 1：**

下面是具有其投影中的单个聚合函数表达式的子查询：

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

查询输出：

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**示例 2**

下面是具有多个聚合函数表达式的子查询：

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

查询输出：

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**示例 3**

下面是包含在投影和筛选器聚合子查询的查询：

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

查询输出：

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

编写此查询的最佳方式是在子查询联接和引用别名中这两个 SELECT 子查询和 WHERE 子句。 此查询是更高效，因为您需要执行子查询仅在联接语句，而不是在投影和筛选器。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

#### <a name="exists-expression"></a>EXISTS 表达式

Azure Cosmos DB 支持 EXISTS 表达式。 这是内置于 Azure Cosmos DB SQL API 聚合标量子查询。 EXISTS 是采用的子查询表达式，如果子查询返回任何行返回 true 的布尔表达式。 否则，返回 false。

由于 Azure Cosmos DB SQL API 不区分布尔表达式和任何其他标量表达式，可以使用在这两个选择的 EXISTS 和 WHERE 子句。 这是与 T-SQL，其中布尔表达式 (如 EXISTS、 BETWEEN 和 IN) 被限制为筛选器不同。

如果 EXISTS 子查询返回有未定义的存在的单个值的计算结果为 false。 例如，考虑下面这个查询的计算结果为 false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果省略前面的子查询中的值关键字，则查询将计算结果为 true:
```sql
SELECT EXISTS (SELECT undefined) 
```

子查询将在对象中的选定列表中包含值的列表。 所选的列表不包含任何值，如果子查询将返回单个值{}。 此值定义，因此 EXISTS 的计算结果为 true。

#### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>示例：重写 ARRAY_CONTAINS 和与 EXISTS 的联接

ARRAY_CONTAINS 一个常见用例是项的要作为筛选依据的一个数组中存在的文档。 在这种情况下，我们检查以查看标记数组是否包含项名为"橙色"。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

您可以重写相同的查询使用 EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

此外，ARRAY_CONTAINS 只能检查值是否等于数组中的任何元素。 如果需要更复杂的筛选器数组属性上，使用联接。

请考虑下面这个查询的筛选器基于的单位和`nutritionValue`数组中的属性： 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

对于每个集合中的文档，其数组元素执行叉积。 此联接操作使得对数组中的属性进行筛选。 但是，此查询的 RU 的使用量将很重要。 例如，如果 1,000 个文档中每个数组有 100 个项，它将扩展到 1000 x 100 (即，100000) 的元组。

使用 EXISTS 可帮助避免此成本高昂的叉积：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在这种情况下，您筛选 EXISTS 子查询中的数组元素。 如果数组元素与匹配筛选器，然后您项目的位置和 EXISTS 的计算结果为 true。

此外可以别名 EXISTS 和在投影中引用它：

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

查询输出：

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

#### <a name="array-expression"></a>数组表达式

可以使用数组表达式以数组形式的查询结果投影。 您可以使用仅在 SELECT 子句中查询的此表达式。

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

查询输出：

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

与其他子查询一样具有数组表达式的筛选器可能会出现。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

查询输出：

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

数组表达式也可以来自中的子查询的 FROM 子句后。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

查询输出：

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>后续步骤

- [SQL 查询示例](how-to-sql-query.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [模型文档数据](modeling-data.md)
