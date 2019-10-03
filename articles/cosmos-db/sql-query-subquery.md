---
title: Azure Cosmos DB 的 SQL 子查询
description: 了解 Azure Cosmos DB 中的 SQL 子查询及其常见用例
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: cea9963f5073834a24ede44306eb89414909fc83
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003478"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB 的 SQL 子查询示例

子查询是嵌套在另一个查询中的查询。 子查询也称为内部查询或内部选择。 包含子查询的语句通常称为外部查询。

本文介绍 Azure Cosmos DB 中的 SQL 子查询及其常见用例。 本文档中的所有示例查询都可以针对预加载在 [Azure Cosmos DB 查询操场](https://www.documentdb.com/sql/demo)上的营养数据集运行。

## <a name="types-of-subqueries"></a>子查询的类型

有两种主要类型的子查询：

* **相关**：引用外部查询中的值的子查询。 将针对外部查询处理的每个行求值该子查询一次。
* **非相关**：独立于外部查询的子查询。 它可以独立运行，而不依赖于外部查询。

> [!NOTE]
> Azure Cosmos DB 仅支持相关子查询。

可以根据子查询返回的行数和列数进一步分类子查询。 有三种类型：
* **Table**：返回多个行和多个列。
* **多值**：返回多个行和单个列。
* **标量**：返回单个行和单个列。

Azure Cosmos DB 中的 SQL 查询始终返回单个列（一个简单的值，或一个复杂文档）。 因此，Azure Cosmos DB 中仅适用多值子查询和标量子查询。 只能在 FROM 子句中将多值子查询用作关系表达式。 可以在 SELECT 或 WHERE 子句中将标量子查询用作标量表达式，或者在 FROM 子句中用作关系表达式。

## <a name="multi-value-subqueries"></a>多值子查询

多值子查询返回一组文档，始终在 FROM 子句中使用。 它们用于：

* 优化 JOIN 表达式。 
* 求值高开销的表达式一次，并多次引用。

## <a name="optimize-join-expressions"></a>优化 JOIN 表达式

多值子查询可以通过在 WHERE 子句中的每个 select-many 表达式后面（而不是所有 cross-join 后面）推送谓词，来优化 JOIN 表达式。

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

对于此查询，索引将匹配包含名为“infant formula”的标记的任一文档。 它是一个值为 0 到 10 的 nutrient 项，以及数量大于 1 的 serving 项。 此处的 JOIN 表达式将针对应用任何筛选器之前的每个匹配文档，执行所有 tags、nutrients 和 servings 数组项的叉积计算。 

然后，WHERE 子句将针对每个 <c, t, n, s> 元组应用筛选谓词。 例如，如果在匹配文档的三个数组中，每个数组包含 10 个项，则此子句将扩展为 1 x 10 x 10 x 10（即 1,000）个元组。 在与下一个表达式联接之前，使用此处的子查询可帮助筛选出联接的数组项。

此查询等效于前面的查询，但使用了子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

假设 tags 数组中只有一个项与筛选器相匹配，而 nutrients 和 servings 数组各有 5 个项。 那么，JOIN 表达式将扩展为 1 x 1 x 5 x 5 = 25 个项，而不是第一个查询中的 1,000 个项。

## <a name="evaluate-once-and-reference-many-times"></a>求值一次，引用多次

子查询可以帮助优化包含高开销表达式（例如，用户定义的函数 (UDF)、复杂字符串或算术表达式）的查询。 可以结合 JOIN 表达式使用子查询，以求值该表达式一次，但引用它多次。

以下查询执行 UDF `GetMaxNutritionValue` 两次：

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

下面是一个等效的查询，但它仅运行该 UDF 一次：

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> 请注意 JOIN 表达式的叉积行为。 如果 UDF 表达式可能会求值为 undefined（未确定），则应该通过从子查询返回对象，而不是直接返回值，来确保 JOIN 表达式始终生成单个行。
>

下面是返回对象而不是返回值的类似示例：

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

此方法并不局限于 UDF， 而是适用于任何可能存在较高开销的表达式。 例如，可以对数学函数 `avg` 采用相同的方法：

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>模拟与外部引用数据的联接

你可能经常需要引用极少发生变化的静态数据，例如度量单位或国家/地区代码。 最好不要为每个文档重复此类数据。 避免这种复制可以节省存储空间，并通过保持较小的文档大小来提高写入性能。 可以使用子查询通过引用数据的集合来模拟内部联接语义。

例如，假设存在以下引用数据集：

| **单位** | **名称**            | **乘数** | **基础单位** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | 纳克            | 1.00E-09       | 克          |
| µg       | 微克           | 1.00E-06       | 克          |
| mg       | 毫克           | 1.00E-03       | 克          |
| g        | 克                | 1.00E+00       | 克          |
| kg       | 千克            | 1.00E+03       | 克          |
| Mg       | 兆克            | 1.00E+06       | 克          |
| Gg       | 千兆克            | 1.00E+09       | 克          |
| nJ       | 纳焦           | 1.00E-09       | 焦耳         |
| µJ       | 微焦          | 1.00E-06       | 焦耳         |
| mJ       | 毫焦          | 1.00E-03       | 焦耳         |
| J        | 焦耳               | 1.00E+00       | 焦耳         |
| kJ       | 千焦           | 1.00E+03       | 焦耳         |
| MJ       | 兆焦           | 1.00E+06       | 焦耳         |
| GJ       | 千兆焦           | 1.00E+09       | 焦耳         |
| cal      | 卡路里             | 1.00E+00       | 卡路里       |
| kcal     | 卡路里             | 1.00E+03       | 卡路里       |
| IU       | 国际单位 |                |               |


以下查询模拟与此数据的联接，以便于将单位名称添加到输出：

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

标量子查询表达式是求值为单个值的子查询。 标量子查询表达式的值是该子查询的投影（SELECT 子句）值。  可以在标量表达式有效的多个位置使用标量子查询表达式。 例如，可以在 SELECT 和 WHERE 子句中的任一表达式内使用标量子查询。

不过，使用标量子查询并不总是有助于优化。 例如，将标量子查询作为参数传递给系统定义的或用户定义的函数并不能在资源单位 (RU) 消耗量与延迟方面带来好处。

标量子查询可以进一步分类为：
* 简单表达式标量子查询
* 聚合标量子查询

## <a name="simple-expression-scalar-subqueries"></a>简单表达式标量子查询

简单表达式标量子查询属于相关子查询，其中的某个 SELECT 子句不包含任何聚合表达式。 这些子查询不能提供优化方面的优势，因为编译器会将其转换为一个较大的简单表达式。 内部与外部查询之间没有相关的上下文。

以下是几个示例：

**示例 1**

```sql
SELECT 1 AS a, 2 AS b
```

可以使用简单表达式标量子查询将此查询重写为：

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

可以使用简单表达式标量子查询将此查询重写为：

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

可以使用简单表达式标量子查询将此查询重写为：

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

聚合标量子查询在其投影或筛选器中包含一个求值为单个值的聚合函数。

**示例 1：**

以下子查询在其投影中包含单个聚合函数表达式：

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

下面是在投影和筛选器中包含聚合子查询的查询：

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

编写此查询的更好方法是在子查询中进行联接，并在 SELECT 和 WHERE 子句中引用子查询别名。 此查询更有效，因为你只需执行 join 语句中的子查询，而无需同时执行投影和筛选器中的子查询。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 表达式

Azure Cosmos DB 支持 EXISTS 表达式。 这是 Azure Cosmos DB SQL API 中内置的聚合标量子查询。 EXISTS 是一个布尔表达式，它采用某个子查询表达式，如果该子查询返回任何行，则 EXISTS 返回 true。 否则返回 false。

由于 Azure Cosmos DB SQL API 不区分布尔表达式和其他任何标量表达式，因此，可以同时在 SELECT 和 WHERE 子句中使用 EXISTS。 T-SQL 则与此不同，其中的布尔表达式（例如 EXISTS、BETWEEN 和 IN）限制为筛选器。

如果 EXISTS 子查询返回未定义的单个值，则 EXISTS 将求值为 false。 例如，假设以下查询求值为 false：
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


如果省略上述子查询中的 VALUE 关键字，则该查询将求值为 true：
```sql
SELECT EXISTS (SELECT undefined) 
```

该子查询在对象中的选定列表内括住值列表。 如果选定的列表没有值，子查询将返回单个值 "{}"。 此值已定义，因此 EXISTS 求值为 true。

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>例如：将 ARRAY_CONTAINS 和 JOIN 重写为 EXISTS

ARRAY_CONTAINS 的一个常见用例是根据某个项在数组中的存在性筛选某个文档。 在本例中，我们将检查 tags 数组是否包含名为“orange”的项。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

可将上述查询重写为使用 EXISTS：

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

此外，ARRAY_CONTAINS 只能检查某个值是否等于数组中的任何元素。 如果需要在数组属性中包含更复杂的筛选器，请使用 JOIN。

假设以下查询根据数组中的 units 和 `nutritionValue` 属性进行筛选： 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

对于集合中的每个文档，将使用其数组元素执行叉积计算。 使用此 JOIN 操作可以根据数组中的属性进行筛选。 但是，此查询的 RU 消耗将很重要。 例如，如果 1,000 个文档包含每个数组中的 100 个项，则它会扩展为 1,000 x 100（即 100,000）个元组。

使用 EXISTS 可以帮助避免这种高开销的叉积计算：

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

在本例中，你将根据 EXISTS 子查询中的数组元素进行筛选。 如果某个数组元素与筛选器匹配，则你需要投影该元素，而 EXISTS 将求值为 true。

还可以指定 EXISTS 的别名，并在投影中引用它：

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

## <a name="array-expression"></a>ARRAY 表达式

可以使用 ARRAY 表达式将查询结果投影为数组。 只能在查询的 SELECT 子句中使用此表达式。

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

与使用其他子查询时一样，可以使用包含 ARRAY 表达式的筛选器。

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

Array 表达式还可以跟在子查询中的 FROM 子句后面。

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
