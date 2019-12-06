---
title: Azure Cosmos DB 的 SQL 子查询
description: 了解 SQL 子查询及其常见用例和不同类型的子查询在 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870558"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 子查询示例

子查询是嵌套在其他查询内的查询。 子查询也称为内部查询或内部选择。 包含子查询的语句通常称为外部查询。

本文介绍了 Azure Cosmos DB 中的 SQL 子查询及其常见用例。 此文档中的所有示例查询均可针对[Azure Cosmos DB 查询操场](https://www.documentdb.com/sql/demo)上预加载的营养数据集运行。

## <a name="types-of-subqueries"></a>子查询的类型

子查询有两种主要类型：

* **相关**：引用外部查询中的值的子查询。 对于外部查询处理的每一行，将计算子查询一次。
* **非相关**：独立于外部查询的子查询。 它可以独立运行，而无需依赖于外部查询。

> [!NOTE]
> Azure Cosmos DB 仅支持相关子查询。

可以根据查询返回的行数和列数进一步对子查询进行分类。 有三种类型：
* **Table**：返回多个行和多个列。
* **多值**：返回多个行和单列。
* **标量**：返回单个行和单列。

Azure Cosmos DB 中的 SQL 查询始终返回单个列（简单值或复杂文档）。 因此，只有多值和标量子查询适用于 Azure Cosmos DB。 只能在 FROM 子句中将多值子查询用作关系表达式。 您可以在 SELECT 或 WHERE 子句中使用标量查询作为标量表达式，或在 FROM 子句中将其用作关系表达式。

## <a name="multi-value-subqueries"></a>多值子查询

多值子查询返回一组文档，始终在 FROM 子句中使用。 它们用于：

* 优化联接表达式。 
* 计算一次昂贵的表达式并多次引用。

## <a name="optimize-join-expressions"></a>优化联接表达式

多值子查询可以优化联接表达式，方法是在每个 select-多个表达式之后（而不是在 WHERE 子句中的所有交叉联接之后）推送谓词。

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

对于此查询，索引将匹配任何标记名称为 "幼儿 formula" 的文档。 它是一个 nutrient 项，其值介于0和10之间，并且为大于1的服务项。 此处的联接表达式将在应用任何筛选器之前，对每个匹配的文档执行标记、nutrients 和 servings 数组的所有项的叉积。 

然后，WHERE 子句会对每个 < c、t、n、s > 元组应用筛选器谓词。 例如，如果匹配文档每三个数组中的每个都有10个项，则它将扩展为 1 x 10 x 10 x 10 （即1000）元组。 使用此处的子查询，可以在联接到下一个表达式之前，帮助筛选出联接的数组项。

此查询等效于前面的查询，但使用子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假设标记数组中只有一个项与筛选器匹配，并且 nutrients 和 servings 数组都有五项。 然后，联接表达式将扩展到 1 x 1 x 5 x 5 = 25 项，而不是第一个查询中的1000项。

## <a name="evaluate-once-and-reference-many-times"></a>计算一次并引用多次

子查询可以帮助优化具有昂贵表达式的查询，例如用户定义的函数（Udf）、复杂字符串或算术表达式。 您可以使用子查询以及联接表达式来计算表达式一次，但多次引用。

以下查询运行两次 UDF `GetMaxNutritionValue`：

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

下面是仅运行 UDF 一次的等效查询：

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 请记住联接表达式的跨产品行为。 如果 UDF 表达式的计算结果为 undefined，则应通过从子查询返回一个对象而不是直接返回值来确保联接表达式始终产生单个行。
>

下面是一个返回对象的示例，而不是返回值：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

此方法并不局限于 Udf。 它适用于任何可能的昂贵表达式。 例如，可以采用与数学函数相同的方法 `avg`：

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>模拟与外部引用数据的联接

可能经常需要引用很少更改的静态数据，如度量单位或国家/地区代码。 最好不要为每个文档重复此类数据。 避免这种重复项将节省存储空间，并通过保持文档大小减小，提高写入性能。 您可以使用子查询来模拟包含引用数据集合的内部联接语义。

例如，请考虑下面这组引用数据：

| **单位** | 名称            | **乘数** | **基本单位** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | 元语法          |
| µg       | Microgram           | 1.00E-06       | 元语法          |
| mg       | Milligram           | 1.00E-03       | 元语法          |
| g        | 元语法                | 1.00E+00       | 元语法          |
| 千克       | 千克            | 1.00 e + 03       | 元语法          |
| Mg       | Megagram            | 1.00E+06       | 元语法          |
| Gg       | Gigagram            | 1.00E+09       | 元语法          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E+00       | Joule         |
| kJ       | Kilojoule           | 1.00 e + 03       | Joule         |
| MJ       | Megajoule           | 1.00E+06       | Joule         |
| GJ       | Gigajoule           | 1.00E+09       | Joule         |
| cal      | 热量             | 1.00E+00       | 热量       |
| kcal     | 热量             | 1.00 e + 03       | 热量       |
| IU       | 国际单位 |                |               |


下面的查询模拟与此数据的联接，以便将单位名称添加到输出：

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

标量子查询表达式是计算结果为单个值的子查询。 标量子查询表达式的值是子查询的投影（SELECT 子句）的值。  可以在很多位置使用标量子查询表达式，其中标量表达式是有效的。 例如，可以在 SELECT 和 WHERE 子句中的任何表达式中使用标量子查询。

不过，使用标量查询并不一定能帮助优化。 例如，将标量子查询作为参数传递给系统或用户定义的函数不会对资源单位（RU）消耗或延迟提供任何好处。

标量子查询可以进一步归类为：
* 简单表达式标量子查询
* 聚合标量子查询

## <a name="simple-expression-scalar-subqueries"></a>简单表达式标量子查询

简单表达式的标量子查询是一个相关子查询，它具有不包含任何聚合表达式的 SELECT 子句。 这些子查询不提供优化优点，因为编译器会将它们转换为一个更大的简单表达式。 内部和外部查询之间没有关联的上下文。

以下是几个示例：

**示例 1**

```sql
SELECT 1 AS a, 2 AS b
```

您可以使用简单表达式的标量子查询将此查询重写为：

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

这两个查询均产生以下输出：

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

您可以使用简单表达式的标量子查询将此查询重写为：

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

您可以使用简单表达式的标量子查询将此查询重写为：

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

聚合标量子查询是一个子查询，其投影或筛选器中的聚合函数的计算结果为单个值。

**示例 1：**

下面是一个子查询，其投影中包含一个聚合函数表达式：

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

下面是包含多个聚合函数表达式的子查询：

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

下面是一个查询，其中包含投影和筛选器中的聚合子查询：

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

若要编写此查询，更好的方法是联接子查询，并同时引用 SELECT 和 WHERE 子句中的子查询别名。 此查询更高效，因为只需在联接语句中执行子查询，而不是在投影和筛选器中执行。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 表达式

Azure Cosmos DB 支持 EXISTS 表达式。 这是 Azure Cosmos DB SQL API 中内置的聚合标量子查询。 EXISTS 是一个布尔表达式，该表达式采用子查询表达式，如果子查询返回任何行，则返回 true。 否则，返回 false。

由于 Azure Cosmos DB SQL API 不区分布尔表达式和任何其他标量表达式，因此可以在 SELECT 和 WHERE 子句中使用 EXISTS。 这不同于 T-sql，其中布尔表达式（例如，EXISTS、BETWEEN 和 IN）仅限筛选器。

如果 EXISTS 子查询返回未定义的单个值，则 EXISTS 的计算结果将为 false。 例如，请考虑以下计算结果为 false 的查询：
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果省略前面的子查询中的 VALUE 关键字，则查询的计算结果将为 true：
```sql
SELECT EXISTS (SELECT undefined) 
```

子查询将在对象的所选列表中包含值列表。 如果选定的列表没有值，子查询将返回单个值 "{}"。 定义此值后，其计算结果为 true。

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>示例：重写 ARRAY_CONTAINS 并将联接作为 EXISTS

ARRAY_CONTAINS 的一个常见用例是通过数组中的某个项来筛选文档。 在这种情况下，我们要检查标记数组是否包含名为 "橙色" 的项。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

您可以重新编写相同的查询以使用 EXISTS：

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

此外，ARRAY_CONTAINS 只能检查某个值是否等于数组中的任何元素。 如果需要更复杂的数组属性筛选器，请使用 JOIN。

请考虑以下查询，该查询基于数组中的单位和 `nutritionValue` 属性进行筛选： 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

对于集合中的每个文档，将使用其数组元素来执行跨产品。 此联接操作使您可以对数组中的属性进行筛选。 但是，此查询的 RU 消耗将很重要。 例如，如果1000的文档在每个数组中有100项，则会将其扩展到 1000 x 100 （即100000）元组。

使用 EXISTS 有助于避免这一价格高昂的跨产品：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在这种情况下，将筛选 EXISTS 子查询内的数组元素。 如果数组元素与筛选器匹配，则将其投影，并且 EXISTS 的计算结果为 true。

你还可以有别名并在投影中引用它：

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

## <a name="array-expression"></a>数组表达式

您可以使用数组表达式将查询结果投影为数组。 只能在查询的 SELECT 子句中使用此表达式。

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

与其他子查询一样，具有数组表达式的筛选器是可能的。

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

数组表达式也可以位于子查询的 FROM 子句之后。

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

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文档数据](modeling-data.md)
