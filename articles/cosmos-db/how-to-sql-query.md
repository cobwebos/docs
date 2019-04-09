---
title: Azure Cosmos DB 的 SQL 查询
description: 了解 Azure Cosmos DB 的 SQL 语法、数据库概念和 SQL 查询。 使用 SQL 作为一种 Azure Cosmos DB JSON 查询语言。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mjbrown
ms.openlocfilehash: 8e5c281a8a8b6c0b48f18bf247b451bf61a7e9dc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263037"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>用于 Azure Cosmos DB 的 SQL 查询示例

Azure Cosmos DB SQL API 帐户支持使用结构化查询语言 (SQL) 用作 JSON 查询语言的查询项。 Azure Cosmos DB 查询语言的设计目标是：

* 支持的最常见和最常用的查询语言，而不是发明一种新查询语言之一的 SQL。 SQL 通过 JSON 项进行丰富的查询提供正式的编程模型。  

* 使用 JavaScript 的编程模型为基础的查询语言。 JavaScript 的类型系统、 表达式计算和函数调用的 SQL API 的根。 这些根提供了自然编程模型功能，如自联接关系投影、 跨 JSON 项的分层导航后，空间查询和完全用 JavaScript 编写的用户定义函数 (Udf) 的调用。

本文逐步讲解一些示例 SQL 查询对简单 JSON 项。 若要了解有关 Azure Cosmos DB SQL 语言语法的详细信息，请参阅[SQL 语法参考](sql-api-query-reference.md)。

## <a id="GettingStarted"></a>开始使用 SQL 查询

在 Cosmos DB SQL API 帐户中，创建名为的容器`Families`。 在容器中创建两个简单的 JSON 项，并针对其运行一些简单的查询。

### <a name="create-json-items"></a>创建 JSON 项

以下代码将创建两个简单的 JSON 项有关系列。 Andersen 和 Wakefield 系列的简单 JSON 项包括父级、 子级和其宠物、 地址和注册信息。 第一项具有字符串、 数字、 布尔值、 数组和嵌套的属性。


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

第二个项使用`givenName`并`familyName`而不是`firstName`和`lastName`。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>查询 JSON 项

请尝试几个查询针对 JSON 数据，了解一些 Azure Cosmos DB 的 SQL 查询语言的主要方面。

以下查询返回的项位置`id`字段匹配`AndersenFamily`。 由于它是`SELECT *`查询，查询的输出是完整的 JSON 项。 有关 SELECT 语法的详细信息，请参阅[SELECT 语句](sql-api-query-reference.md#select-query)。 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查询结果将是： 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

下面的查询重新设置 JSON 输出格式到不同的形状。 该查询投影新 JSON`Family`包含两个所选字段对象`Name`和`City`，当地址城市时的状态相同。 "NY，NY"匹配这种情况。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查询结果将是：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

下面的查询返回的子级的所有给定名称系列中其`id`匹配`WakefieldFamily`、 有序的等级。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.grade ASC
```

其结果是：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

前述示例介绍了 Cosmos DB 查询语言的几个方面：  

* 由于 SQL API 适用于 JSON 值，它可以处理与树形实体而不是行和列。 可以参考的树节点在任意深度，如`Node1.Node2.Node3…..Nodem`，类似于两个部分引用的`<table>.<column>`ANSI SQL 中。

* 查询语言适用于无架构数据，因为必须动态绑定的类型系统。 相同的表达式在不同项上可能会产生不同的类型。 查询的结果是一个有效的 JSON 值，但不保证是固定的架构。  

* Azure Cosmos DB 仅支持严格的 JSON 项。 类型系统和表达式只限于仅处理 JSON 类型。 有关详细信息，请参阅[JSON 规范](https://www.json.org/)。  

* Cosmos DB 容器是 JSON 项的一个无架构集合。 内和跨容器项的关系是隐式捕获包含，不通过主键和外键关系。 此功能很重要的内部项联接在本文后面部分所述。

## <a id="SelectClause"></a>SELECT 子句

每个查询包括 SELECT 子句和可选的 FROM 和 WHERE 子句，每个 ANSI SQL 标准。 通常情况下，枚举时在 FROM 子句中的源，并在 WHERE 子句应用到源以检索 JSON 项的子集筛选器。 然后，SELECT 子句投影选择列表中请求的 JSON 值。 有关语法的详细信息，请参阅[SELECT 语句](sql-api-query-reference.md#select-query)。

下面的 SELECT 查询的示例返回`address`从`Families`其`id`匹配`AndersenFamily`:

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

## <a id="EscapingReservedKeywords"></a>带引号的属性访问器
您可以访问使用带引号的属性运算符 [] 的属性。 例如，`SELECT c.grade` 和 `SELECT c["grade"]` 是等效的。 此语法可用于转义包含空格和特殊字符，或具有相同的名称作为 SQL 关键字或保留的字的属性。

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>嵌套属性

下面的示例项目的两个嵌套的属性，`f.address.state`和`f.address.city`。

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

## <a name="json-expressions"></a>JSON 表达式

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

在上述示例中，SELECT 子句需要创建一个 JSON 对象，和子句的示例提供不了任何键，因为使用隐式参数变量名`$1`。 以下查询将返回两个隐式参数变量：`$1`和`$2`。

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

## <a id="ValueKeyword"></a>VALUE 关键字

VALUE 关键字提供了一种返回 JSON 值单独方法。 例如，如下所示的查询返回的标量表达式`"Hello World"`而不是`{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

下面的查询返回的 JSON 值，而无需`address`标签：

```sql
    SELECT VALUE f.address
    FROM Families f
```

其结果是：

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

下面的示例演示如何返回 JSON 基元值 （JSON 树的叶级别）：


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

其结果是：

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="aliasing"></a>别名

可以使用显式别名在查询中的值。 如果查询包含两个具有相同的名称，使用别名重命名一个或两个属性，因此它们在投影的结果中消除歧义。

As 关键字用于别名是可选的如下面的示例中所示，投影为第二个值时`NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

其结果是：

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM 子句

FROM (`FROM <from_specification>`) 子句是可选的除非源进行筛选或更高版本在查询中进行投影。 有关语法的详细信息，请参阅[语法从](sql-api-query-reference.md#bk_from_clause)。 之类的查询`SELECT * FROM Families`枚举整个`Families`容器。 而不是使用容器名称的容器，还可以使用特殊标识符 ROOT。

FROM 子句强制实施每个查询的以下规则：

* 容器可以使用别名，如 `SELECT f.id FROM Families AS f` 或只需为 `SELECT f.id FROM Families f`。 这里`f`是别名`Families`。 如是一个可选的关键字为别名的标识符。  

* 一旦有了别名，无法绑定原始的源名称。 例如，`SELECT Families.id FROM Families f`在语法上无效因为标识符`Families`的别名已且无法再解析。  

* 被引用的所有属性必须都是完全限定，以避免在严格的架构的遵从性没有任何不确定的绑定。 例如，`SELECT id FROM Families f`在语法上无效因为属性`id`没有绑定。

### <a name="get-subitems-by-using-the-from-clause"></a>获取子项的 FROM 子句

FROM 子句可以减少到较小子集的源。 若要枚举仅子树中的每个项，则子根可以变成源，，如下面的示例中所示：

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

上述查询中使用数组作为源，但您还可以使用对象作为源。 该查询将视为包含在结果中的源中的任何有效的、 定义 JSON 值。 以下示例将排除`Families`没有`address.state`值。

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

## <a id="WhereClause"></a>WHERE 子句

可选的 WHERE 子句 (`WHERE <filter_condition>`) 指定条件的源 JSON 项必须满足要包括在结果中的查询。 JSON 项到指定的条件的计算结果必须`true`以作为结果。 索引层使用 WHERE 子句，以确定最小可以是结果的一部分的源项的子集。 有关语法的详细信息，请参阅[WHERE 语法](sql-api-query-reference.md#bk_where_clause)。

下面的查询请求包含的项`id`值为的属性`AndersenFamily`。 它不包括不具有任何项`id`属性或其值不匹配`AndersenFamily`。

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

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 子句中的标量表达式

上面的示例演示了一个简单的等式查询。 SQL API 还支持各种[标量表达式](#scalar-expressions)。 最常使用的是二进制和一元表达式。 来自源 JSON 对象的属性引用也是有效的表达式。

可以使用以下受支持的二进制运算符：  

|**运算符类型**  | **值** |
|---------|---------|
|算术 | +,-,*,/,% |
|位    | \|、&、^、<<、>>、>>>（补零右移） |
|逻辑    | AND、OR、NOT      |
|比较 | =、!=、&lt;、&gt;、&lt;=、&gt;=、<> |
|String     |  \|\|（连接） |

以下查询使用二进制运算符：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

此外可以使用一元运算符 +、-、 ~，而不是在查询，如以下示例所示：

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

此外可以在查询中使用属性引用。 例如，`SELECT * FROM Families f WHERE f.isRegistered`返回包含该属性的 JSON 项`isRegistered`值等于`true`。 任何其他值，例如`false`， `null`， `Undefined`， `<number>`， `<string>`， `<object>`，或`<array>`，从结果中排除项。 

### <a name="equality-and-comparison-operators"></a>等式和比较运算符

下表显示了 SQL API 中任意两个 JSON 类型之间等式比较的结果。

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **对象** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **正常** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **正常** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **正常** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **正常** | Undefined | Undefined |
| **对象** | Undefined | Undefined | Undefined | Undefined | Undefined | **正常** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **正常** |

对于比较运算符，如`>`， `>=`， `!=`， `<`，并`<=`，比较在类型或两个对象或数组生成`Undefined`。  

标量表达式的结果是否`Undefined`，该项目不包括在结果中，因为`Undefined`不等于`true`。

### <a name="logical-and-or-and-not-operators"></a>逻辑（AND、OR 和 NOT）运算符

逻辑运算符对布尔值进行运算。 下表显示了这些运算符的逻辑真值表：

**或运算符**

| 或 | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND 运算符**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT 运算符**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

## <a name="between-keyword"></a>BETWEEN 关键字

与 ANSI SQL 中，可以使用 BETWEEN 关键字来对字符串或数字值的范围执行快速查询。 例如，以下查询将返回第一个子女的年级 1-5，非独占的所有项。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

与在 ANSI SQL 中，您还可以使用 BETWEEN 子句在 FROM 子句中，如以下示例所示。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API，与 ANSI SQL 中可以执行快速范围查询对混合类型的属性。 例如，`grade`可能是一个数字与类似`5`某些项和之类的字符串中`grade4`在其他用例。 在这些情况下，与 JavaScript，两种不同类型之间的比较会导致`Undefined`，因此跳过项。

> [!TIP]
> 更快的查询执行时间，用于创建使用范围索引类型 BETWEEN 子句将筛选任何数值属性或路径的索引策略。

## <a name="in-keyword"></a>IN 关键字

使用 IN 关键字来检查指定的值是否与列表中的任何值相匹配。 例如，以下查询返回家族的所有项，`id`是`WakefieldFamily`或`AndersenFamily`。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

下面的示例返回所有项的状态为任何指定的值：

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* 运算符

特殊运算符 * 项目整个项原样。 在使用时，它必须仅为投影的字段。 之类的查询`SELECT * FROM Families f`有效，但`SELECT VALUE * FROM Families f`和`SELECT *, f.id FROM Families f`无效。 [这篇文章中的第一次查询](#query-the-json-items)使用 * 运算符。 

## <a name="-and--operators"></a>? 和?? 运算符

您可以使用三元 （？） 和 Coalesce （？） 运算符来生成条件表达式，如下所示编程语言，如C#和 JavaScript。 

可以使用？ 若要构造动态的新 JSON 属性的运算符。 例如，以下查询将分类到级级别`elementary`或`other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

此外可以嵌套调用？ 运算符，如下面的查询中所示： 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

与其他查询运算符，一样？ 如果引用的属性缺失或正在进行比较的类型不同，运算符将排除项。

使用?? 若要针对半结构化或混合类型的数据进行查询时有效地检查中项的属性的运算符。 例如，以下查询将返回`lastName`（如果存在） 或`surname`如果`lastName`不存在。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP 运算符

TOP 关键字返回第一个`N`不确定的顺序的查询结果数。 最佳做法是，使用 TOP 与 ORDER BY 子句来将结果限制为第一个`N`有序值的数目。 结合这两个子句是可预测指示哪些行 TOP 影响的唯一方法。 

您可以使用 TOP 与常量值，如以下示例中，或使用参数化的查询变量的值。 有关详细信息，请参阅[参数化查询](#parameterized-queries)部分。

```sql
    SELECT TOP 1 *
    FROM Families f
```

其结果是：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY 子句

与 ANSI SQL 中，可以在查询中包括可选的 ORDER BY 子句。 可选的 ASC 或 DESC 参数指定是否检索结果按升序或降序排序。 默认值为 ASC。

例如，下面是名称的检索按居住城市的升序的家庭的查询：

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

其结果是：

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

下面的查询检索系列`id`中其项的创建日期的顺序。 项`creationDate`一个数字表示*纪元时间*，或自 1970 年 1 月 1 日以秒为单位起经过的时间。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

其结果是：

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
## <a name="scalar-expressions"></a>标量表达式

SELECT 子句支持标量表达式，如常量、 算术表达式和逻辑表达式。 下面的查询使用的标量表达式：


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

其结果是：

```json
    [{
      "$1": 1.33333
    }]
```

在以下查询中，标量表达式的结果是一个布尔值：


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

其结果是：

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>对象和数组创建

SQL API 的一个重要功能是数组和对象创建。 前面的示例创建一个新的 JSON 对象， `AreFromSameCityState`。 您还可以构造数组，如下面的示例中所示：


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

其结果是：

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```


## <a id="Iteration"></a>迭代

SQL API 提供支持，以为遍历 JSON 数组，与通过 IN 关键字 FROM 源中添加新的构造。 在下面的示例：

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
          "pets": [{ "givenName": "Fluffy"}]
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

下一个查询对执行迭代`children`在`Families`容器。 输出数组是不同于前面的查询。 此示例拆分`children`，并将结果合并为一个数组：  

```sql
    SELECT *
    FROM c IN Families.children
```

其结果是：

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
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
```

您可以筛选进一步单个实体的数组，如下面的示例中所示：

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

其结果是：

```json
    [{
      "givenName": "Lisa"
    }]
```

你还可以通过数组迭代的结果进行聚合。 例如，以下查询计算所有家庭的孩子的数量：

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

其结果是：

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>联接

在关系数据库中，跨表联接都是逻辑要求设计规范化的架构。 SQL API 与此相反，使用非规范化的数据模型的架构项，这是逻辑的等效*自联接*。

支持的语言的语法`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`。 此查询将返回一组元组与`N`值。 每个元组拥有通过对它们相应的集遍历所有容器别名所产生的值。 换言之，此查询是参与联接的集的完整叉积。

下面的示例演示了 JOIN 子句是如何工作的。 在以下示例中，结果为空，因为源中的每个项的叉积和一个空集为空：

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

结果为：

```json
    [{
    }]
```

在以下示例中，联接位于两个 JSON 对象，项根之间的叉积`id`和`children`子根。 这一事实，`children`数组不是有效联接，因为它可以处理是一个根`children`数组。 结果包含只有两个结果，因为包含数组的每个项目的叉积生成恰好只有一个项。

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

其结果是：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

下面的示例演示了更传统的联接：

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

其结果是：

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 子句的 FROM 源是一个迭代器。 因此，前面的示例中的流是：  

1. 展开每个子元素`c`数组中。
2. 将应用与该项目的根的叉积`f`其中每个子元素`c`平展的第一步。
3. 最后，项目的根对象`f``id`单独的属性。

第一项`AndersenFamily`，只包含一个`children`元素，因此结果集包含单个对象。 第二项`WakefieldFamily`，包含两个`children`，因此，叉积生成两个对象，每个`children`元素。 这两个项中的根字段会是相同的，正如在叉积中所预期的一样。

JOIN 子句的真正的实用程序是窗体的元组从叉积，否则很难对项目形状中。 下面的允许用户选择的总体元组满足的条件的元组组合的筛选器的示例。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

其结果是：

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

前面的示例中的以下扩展执行双联接。 您无法将叉积视为下面的伪代码：

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` 有一个子拥有一只宠物，因此叉积生成一行 (1\*1\*1) 从这一系列。 `WakefieldFamily` 有两个孩子，只有一个用户拥有宠物，但该子级有 2 只宠物。 此系列叉积生成 1\*1\*2 = 2 行。

在下一步的示例中，一个附加筛选器上没有`pet`，其中排除了宠物名称不是所有元组`Shadow`。 您可以生成数组，该元组元素中的任何筛选器中的元组和项目的元素的任意组合。

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

其结果是：

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>用户定义函数 (Udf)

SQL API 为用户定义函数 (Udf) 提供支持。 使用标量 Udf，可以传入了零个或多个参数并返回单个参数结果。 API 检查每个自变量的合法的 JSON 值。  

API 扩展 SQL 语法以支持使用 Udf 的自定义应用程序逻辑。 可以使用 SQL API 注册 Udf，并在 SQL 查询中引用它们。 事实上，UDF 经过精心设计，可从查询调用。 按照推论，Udf 不具有对上下文对象与其他 JavaScript 类型，例如存储的过程和触发器一样的访问权限。 查询是只读的并且可以运行在主要或次要副本上。 Udf，与其他 JavaScript 类型，用于在辅助副本上运行。

下面的示例将 Cosmos DB 数据库中下一个项容器, 的 UDF。 此示例将创建其名称是的 UDF `REGEX_MATCH`。 它接受两个 JSON 字符串值`input`并`pattern`，并检查第一个匹配项中第二个指定的模式使用 JavaScript 的`string.match()`函数。

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

现在，使用此 UDF 的查询投影。 您必须用区分大小写的前缀限定 Udf`udf.`调用从查询中时。

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

可以使用限定 UDF`udf.`前缀中的筛选器，如以下示例所示：

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

从本质上讲，Udf 是可以使用投影和筛选器中的有效标量表达式。

若要扩展 Udf 的强大功能，使用条件逻辑看另一个示例：

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

下面的示例 udf 的：

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

如果引用的属性由 UDF 参数中未提供的 JSON 值，该参数被视为未定义，将跳过 UDF 调用。 同样，如果未定义 UDF 的结果，则它不是包括在结果中。

如前面的示例所示，Udf 使用 SQL API 集成 JavaScript 语言的强大功能。 Udf 提供丰富的可编程接口执行复杂的过程，条件逻辑内置 JavaScript 运行时功能的帮助。 SQL API 在提供参数的 udf 的每个源项的当前的位置或 SELECT 子句的处理阶段。 结果无缝地纳入总体执行管道。 总之，Udf 是强大的工具来执行复杂的业务逻辑操作作为查询的一部分。

## <a id="Aggregates"></a>聚合函数

聚合函数对一组在 SELECT 子句中的值执行计算并返回单个值。 例如，以下查询返回的中的项计数`Families`容器：

```sql
    SELECT COUNT(1)
    FROM Families f
```

其结果是：

```json
    [{
        "$1": 2
    }]
```

此外可以通过使用 VALUE 关键字来返回仅聚合的标量值。 例如，以下查询将值的计数作为单个值返回：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

其结果是：

```json
    [ 2 ]
```

也可以使用筛选器组合的聚合。 例如，以下查询将返回与的地址状态项的计数`WA`。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

其结果是：

```json
    [ 1 ]
```

SQL API 支持以下聚合函数。 SUM 和 AVG 对数字值，并计数、 最小和最大对数字、 字符串、 布尔值和 null 值。

| 函数 | 描述 |
|-------|-------------|
| COUNT | 在表达式中返回项的数目。 |
| SUM   | 在表达式中返回所有值的总和。 |
| 最小值   | 在表达式中返回最小值。 |
| MAX   | 在表达式中返回最大值。 |
| 平均值   | 在表达式中返回多个值的平均值。 |

你还可以对数组迭代的结果进行聚合。 有关详细信息，请参阅[迭代](#Iteration)部分。

> [!NOTE]
> 在 Azure 门户数据资源管理器中聚合查询可能会随只有一个查询页聚合部分结果。 SDK 跨所有页面生成单个累计值。 若要执行聚合查询使用代码，需要.NET SDK 1.12.0、.net、.NET Core SDK 1.1.0，或者 Java SDK 1.9.5 或更高版本。
>

## <a id="BuiltinFunctions"></a>内置函数

Cosmos DB 还支持多个内置函数进行常见操作，则可以在与用户定义函数 (Udf) 类似的查询内部使用。

| 函数组 | 操作 |
|---------|----------|
| 数学函数 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN 和 TAN |
| 类型检查函数 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED 和 IS_PRIMITIVE |
| 字符串函数 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING 和 UPPER |
| 数组函数 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE |
| 空间函数 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID 和 ST_ISVALIDDETAILED |

如果您当前正使用用户定义的函数 (UDF) 为其内置函数现已推出，相应的内置函数将运行更快、 更高效。

Cosmos DB 函数和 ANSI SQL 函数的主要区别是，Cosmos DB 函数旨在很好地配合无架构和混合架构数据。 例如，如果属性缺失或具有非数字值，如`unknown`，项会跳过而不是返回错误。

### <a name="mathematical-functions"></a>数学函数

每个数学函数均执行一个计算，基于作为参数提供的输出值，并返回数值。 以下是受支持的内置数学函数表。

| 使用情况 | 描述 |
|----------|--------|
| ABS (num_expr) | 返回指定数值表达式的绝对（正）值。 |
| CEILING (num_expr) | 返回大于或等于指定数值表达式的最小整数值。 |
| FLOOR (num_expr) | 返回小于或等于指定数值表达式的最大整数。 |
| EXP (num_expr) | 返回指定数值表达式的指数。 |
| LOG (num_expr, base) | 返回指定数值表达式或使用指定的底的对数的自然对数。 |
| LOG10 (num_expr) | 返回指定数值表达式以 10 为底的对数值。 |
| ROUND (num_expr) | 返回一个数值，四舍五入到最接近的整数值。 |
| TRUNC (num_expr) | 返回一个数值，截断到最接近的整数值。 |
| SQRT (num_expr) | 返回指定数值表达式的平方根。 |
| SQUARE (num_expr) | 返回指定数值表达式的平方。 |
| POWER (num_expr, num_expr) | 返回指定数值表达式相对指定值的幂。 |
| SIGN (num_expr) | 返回指定数值表达式的符号值 (-1, 0, 1)。 |
| ACOS (num_expr) | 返回角度（弧度），其余弦是指定的数值表达式；也被称为反余弦。 |
| ASIN (num_expr) | 返回角度（弧度），其正弦是指定的数值表达式。 此函数也称为反正弦。 |
| ATAN (num_expr) | 返回角度（弧度），其正切是指定的数值表达式。 此函数也被称为反正切值。 |
| ATN2 (num_expr) | 返回正 x 轴与射线（原点到点 (y, x)）之间的角度（弧度），其中 x 和 y 是两个指定的浮点表达式的值。 |
| COS (num_expr) | 返回指定表达式中指定角度的三角余弦（弧度）。 |
| COT (num_expr) | 返回指定数值表达式中指定角度的三角余切。 |
| DEGREES (num_expr) | 返回指定角度（弧度）的相应角度（度）。 |
| PI () | 返回 PI 的常数值。 |
| RADIANS (num_expr) | 返回输入的数值表达式（度）的弧度。 |
| SIN (num_expr) | 返回指定表达式中指定角度的三角正弦（弧度）。 |
| TAN (num_expr) | 返回指定表达式中输入表达式的正切。 |

您可以运行查询，如下例所示：

```sql
    SELECT VALUE ABS(-4)
```

结果为：

```json
    [4]
```

### <a name="type-checking-functions"></a>类型检查函数

类型检查函数使您可以检查 SQL 查询中表达式的类型。 类型检查函数可用于在变量或未知时确定在动态，项中的属性的类型。 下面是支持的内置类型检查函数表：

| **使用情况** | **描述** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | 返回一个布尔值，它指示值的类型是否为数组。 |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | 返回一个布尔值，它指示值的类型是否为布尔。 |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | 返回一个布尔值，它指示值的类型是否为 null。 |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | 返回一个布尔值，它指示值的类型是否为数字。 |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | 返回一个布尔值，它指示值的类型是否为 JSON 对象。 |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | 返回一个布尔值，它指示值的类型是否为字符串。 |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | 返回一个布尔，它指示属性是否已经分配了值。 |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | 返回一个布尔值，该值指示是否的值的类型字符串、 数字、 布尔值或为 null。 |

使用这些函数，可以运行查询，如下例所示：

```sql
    SELECT VALUE IS_NUMBER(-4)
```

结果为：

```json
    [true]
```

### <a name="string-functions"></a>字符串函数

以下标量函数对字符串输入值执行运算并返回一个字符串，数值或布尔值。 以下是内置字符串函数表：

| 使用情况 | 描述 |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | 返回指定字符串表达式的字符数。 |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | 返回一个字符串，该字符串是连接两个或多个字符串值的结果。 |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | 返回部分字符串表达式。 |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。 |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。 |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | 返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。 |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | 返回第二个的第一个匹配项的起始位置字符串表达式中第一个指定的字符串表达式，则为-1，如果找不到字符串。 |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | 返回具有指定字符数的字符串的左侧部分。 |
| [右 （str_expr，num_expr）](sql-api-query-reference.md#bk_right) | 返回具有指定字符数的字符串的右侧部分。 |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | 返回删除前导空格后的字符串表达式。 |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | 返回截断所有尾随空格后的字符串表达式。 |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | 返回在将大写字符数据转换为小写后的字符串表达式。 |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | 返回在将小写字符数据转换为大写后的字符串表达式。 |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | 将出现的所有指定字符串值替换为另一个字符串值。 |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | 将一个字符串值重复指定的次数。 |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | 返回字符串值的逆序排序形式。 |

使用这些函数，可以运行以下命令，它将返回系列查询`id`以大写形式：

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

其结果是：

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

或连接字符串，如在此示例中：

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

其结果是：

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

此外可以使用 WHERE 子句来筛选结果，如在下面的示例中的字符串函数：

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

其结果是：

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>数组函数

以下标量函数对数组输入值执行运算并返回数值、 布尔值或数组值。 以下是内置数组函数表：

| 使用情况 | 描述 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |返回指定数组表达式的元素数。 |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |返回一个数组，该数组是连接两个或更多数组值的结果。 |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |返回一个布尔，它指示数组是否包含指定的值。 可以指定是要执行完全还是部分匹配。 |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |返回部分数组表达式。 |

使用数组函数 JSON 内操纵数组。 例如，下面是返回所有项的查询`id`s 一个的`parents`是`Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

结果为：

```json
    [{
      "id": "WakefieldFamily"
    }]
```

可以指定一个部分片段来匹配数组中的元素。 以下查询将查找所有项`id`具有`parents`与`givenName`的`Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

结果为：

```json
    [{
      "id": "WakefieldFamily"
    }]
```

下面是使用 ARRAY_LENGTH 获取的数字的另一个示例`children`每个系列：

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

其结果是：

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>空间函数

Cosmos DB 支持以下开放地理空间信息联盟 (OGC) 内置函数用于地理空间查询： 

| 使用情况 | 描述 |
| --- | --- |
| ST_DISTANCE (point_expr、point_expr) | 返回两个 GeoJSON 之间的距离`Point`， `Polygon`，或`LineString`表达式。 |
| T_WITHIN (point_expr, polygon_expr) | 返回一个布尔表达式，该值指示是否第一个 GeoJSON 对象 (`Point`， `Polygon`，或`LineString`) 内的第二个 GeoJSON 对象 (`Point`， `Polygon`，或`LineString`)。 |
| ST_INTERSECTS (spatial_expr, spatial_expr) | 返回一个布尔表达式，该值指示是否两个指定的 GeoJSON 对象 (`Point`， `Polygon`，或`LineString`) 相交。 |
| ST_ISVALID | 返回一个布尔值，该值指示是否指定的 GeoJSON `Point`， `Polygon`，或`LineString`表达式是有效的。 |
| ST_ISVALIDDETAILED | 返回一个 JSON 值，该值包含一个布尔值，如果指定的 GeoJSON `Point`， `Polygon`，或`LineString`表达式是有效的如果无效，原因作为字符串值。 |

空间函数可用于执行对空间数据执行邻近查询。 例如，下面是返回的使用 ST_DISTANCE 内置函数的指定位置 30 km 内的所有系列项的查询：

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

结果为：

```json
    [{
      "id": "WakefieldFamily"
    }]
```

有关 Cosmos DB 中地理支持的详细信息，请参阅[在 Azure Cosmos DB 中使用地理数据](geospatial.md)。 

## <a name="parameterized-queries"></a>参数化的查询

Cosmos DB 支持使用通过常用的 @ 表示法表示的参数进行查询。 参数化的 SQL 提供了可靠的处理和转义的用户输入，并防止通过 SQL 注入的数据的意外泄露。

例如，可以编写一个查询，采用`lastName`并`address.state`作为参数，并执行它的各种值`lastName`和`address.state`根据用户输入。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

然后可以将此请求发送到 Cosmos DB，作为参数化 JSON 查询如下所示：

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

下面的示例设置的参数化查询的顶级参数： 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

参数值可以是任何有效的 JSON： 字符串、 数字、 布尔值为 null，甚至是数组或嵌套的 JSON。 由于 Cosmos DB 是无架构，因此参数不被验证对任何类型。

## <a id="JavaScriptIntegration"></a>JavaScript 集成

Azure Cosmos DB 会直接在容器上执行基于 JavaScript 的应用程序逻辑提供编程模型，使用存储的过程和触发器。 此模型支持：

* 高性能事务性 CRUD 操作和针对 JavaScript 运行时数据库引擎内深度集成的容器中的项的查询。
* 控制流、 变量作用域，并分配和集成的异常处理基元与数据库事务自然建模。 

有关 Azure Cosmos DB JavaScript 集成的详细信息，请参阅[JavaScript 服务器端 API](#JavaScriptServerSideApi)部分。

### <a name="operator-evaluation"></a>运算符评估

由于是一个 JSON 数据库，cosmos DB 中，绘制与 JavaScript 运算符以及求值语义。 Cosmos DB 尝试保留 JavaScript 语义就 JSON 支持而言，但操作评估在某些实例中有所偏移。

在 SQL API 中，与不同在传统 SQL 中的值类型经常是未知直到 API 从数据库检索的值。 为了高效执行查询，大多数运算符具有严格的类型要求。

与 JavaScript 不同，SQL API 不会执行隐式转换。 例如，之类的查询`SELECT * FROM Person p WHERE p.Age = 21`匹配项，包含`Age`值为的属性`21`。 它不匹配任何其他项的`Age`属性与匹配类可能无限变化`twenty-one`， `021`，或`21.0`。 这与 JavaScript 中，其中字符串值隐式转换为数字根据运算符，例如冲突： `==`。 此 SQL API 行为的高效索引匹配至关重要。

## <a id="ExecutingSqlQueries"></a>执行 SQL 查询

任何可以发出 HTTP/HTTPS 请求的语言可以调用 Cosmos DB REST API。 Cosmos DB 还提供了用于.NET、 Node.js、 JavaScript 和 Python 编程语言的编程库。 REST API 和库都支持通过 SQL、 查询和.NET SDK 还支持[LINQ 查询](#Linq)。

以下示例演示了如何对 Cosmos DB 数据库帐户创建查询并提交。

### <a id="RestAPI"></a>REST API

Cosmos DB 通过 HTTP 提供开放的 RESTful 编程模型。 资源模型包含一组数据库帐户下的资源的 Azure 订阅预配。 数据库帐户都包含一系列*数据库*，其中每个可以包含多个*容器*，后者又包含*项*，Udf 和其他资源类型。 每个 Cosmos DB 资源进行寻址使用逻辑和稳定的 URI。 一组资源称为*馈送*。 

使用这些资源的基本交互模型是通过 HTTP 谓词`GET`， `PUT`， `POST`，和`DELETE`，使用其标准解释。 使用`POST`若要创建新的资源，执行存储的过程或发出 Cosmos DB 查询。 查询始终为只读操作，且无任何副作用。

下面的示例演示`POST`SQL API 查询的示例项。 该查询将简单的筛选器对 JSON`name`属性。 `x-ms-documentdb-isquery`和内容类型：`application/query+json`标头表示该操作是一个查询。 替换为`mysqlapicosmosdb.documents.azure.com:443`与 Cosmos DB 帐户的 URI。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

其结果是：

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

接下来，更复杂的查询返回通过联接多个结果：

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

其结果是： 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

如果查询的结果无法容纳在单个页面中，REST API 将返回的继续标记通过`x-ms-continuation-token`响应标头。 客户端可以通过在后续结果中包含标头对结果进行分页。 此外可以控制每页查看的结果数`x-ms-max-item-count`号标头。 

如果查询具有 COUNT 等聚合函数，查询页可能会通过只有一个结果页返回部分聚合的值。 客户端必须对这些结果以生成最终结果执行二级聚合。 例如，对各个页面，以便返回总的计数中返回的计数的总和。

若要管理查询的数据一致性策略，请使用`x-ms-consistency-level`如下所示所有 REST API 请求标头。 会话一致性还需要回显最新`x-ms-session-token`查询请求的 cookie 标头。 查询容器的索引策略也可以影响查询结果的一致性。 使用默认的索引策略设置的容器，索引始终是当前项内容，并且查询结果与为数据选择的一致性匹配。 有关详细信息，请参阅[Azure Cosmos DB 一致性级别][consistency-levels]。

如果在容器上配置的索引策略不能支持指定的查询，Azure Cosmos DB 服务器将返回 400"错误的请求"。 此错误消息的查询返回其从索引中显式排除的路径。 您可以指定`x-ms-documentdb-query-enable-scan`标头以允许查询索引不可用时执行扫描。

可以通过设置查询执行中获取详细的指标`x-ms-documentdb-populatequerymetrics`标头与`true`。 有关详细信息，请参阅 [Azure Cosmos DB 的 SQL 查询指标](sql-api-query-metrics.md)。

### <a id="DotNetSdk"></a>C# (.NET SDK)

.NET SDK 支持 LINQ 和 SQL 查询。 下面的示例演示如何执行上述筛选器查询使用.NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

以下示例将每一项内相等的两个属性进行比较，并使用匿名投影。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

下面的示例演示通过 LINQ 表示的联接`SelectMany`。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 客户端自动遍历的查询结果中的所有页`foreach`阻止，如前面的示例中所示。 中引入的查询选项[REST API](#RestAPI)部分也会出现在.NET SDK 中，使用`FeedOptions`并`FeedResponse`中的类`CreateDocumentQuery`方法。 您可以使用来控制页面数`MaxItemCount`设置。

还可以通过使用 `IQueryable` 对象创建 `IDocumentQueryable`，并读取 ` ResponseContinuationToken` 值并将它们作为 `FeedOptions` 中的 `RequestContinuationToken` 向回传递，从而显式控制分页。 可以设置`EnableScanInQuery`查询不支持配置的索引策略时启用扫描。 对于已分区容器，可以使用`PartitionKey`来针对单个分区运行查询，尽管 Azure Cosmos DB 可以自动提取这从查询文本。 可以使用`EnableCrossPartitionQuery`对多个分区运行查询。

有关使用查询的更多.NET 示例，请参阅[Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)在 GitHub 中。

### <a id="JavaScriptServerSideApi"></a>JavaScript 服务器端 API

Cosmos DB 容器，使用存储的过程和触发器上直接执行基于 JavaScript 应用程序逻辑提供编程模型。 在容器级注册，JavaScript 逻辑稍后可以发出数据库操作在给定容器，包装在周围 ACID 事务的项。

下面的示例演示如何使用`queryDocuments`JavaScript 服务器 API 来简化从查询中内部存储过程和触发器：

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>SQL API 的 LINQ

LINQ 是一个计算表示为对象流的查询的.NET 编程模型。 Cosmos DB 提供一个客户端库，通过促进 JSON 与 .NET 对象之间的转换，以及从 LINQ 查询的子集到 Cosmos DB 查询的映射，来与 LINQ 进行交互。

下图显示了使用 Cosmos DB 支持 LINQ 查询的体系结构。 使用 Cosmos DB 客户端，您可以创建`IQueryable`直接查询 Cosmos DB 查询提供程序，并将 LINQ 查询转换为 Cosmos DB 查询的对象。 然后将查询传递到 Cosmos DB 服务器检索一组 JSON 格式的结果。 JSON 反序列化程序将结果转换成流的客户端上的.NET 对象。

![支持使用 SQL API 的 LINQ 查询的体系结构 - SQL语法、JSON 查询语言、数据库概念和 SQL 查询][1]

### <a name="net-and-json-mapping"></a>.NET 和 JSON 映射

.NET 对象与 JSON 项之间的映射是自然。 每个数据成员字段映射到 JSON 对象，其中字段名称将映射到*键*一部分的对象，并且值以递归方式映射到*值*对象的一部分。 下面的代码映射`Family`到 JSON 项类，然后创建`Family`对象：

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

前面的示例将创建以下 JSON 项：

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>LINQ 到 SQL 转换

Cosmos DB 查询提供程序执行从 LINQ 查询到 Cosmos DB SQL 查询的最有效映射。 以下说明假定你基本熟悉 LINQ。

查询提供程序类型系统仅支持 JSON 基元类型： 数字、 布尔值，字符串，并为 null。 

查询提供程序支持的标量表达式：

- 常量值，包括评估查询时基元数据类型的常量值。
  
- 引用的对象或数组元素的属性的属性/数组索引表达式。 例如：
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算术表达式，包括数字和布尔值的常用算术表达式。 有关完整列表，请参阅[Azure Cosmos DB SQL 规范](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字符串比较表达式，其中包括比较字符串值与某些常量字符串值。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 对象/数组创建表达式，返回复合值类型或匿名类型的对象或此类对象的数组。 您可以嵌套这些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>支持的 LINQ 运算符

SQL.NET SDK 中包含的 LINQ 提供程序支持以下运算符：

- **Select**：投影转换为 SQL SELECT，包括对象构造。
- **Where**：筛选器转换为 SQL WHERE，且支持`&&`， `||`，和`!`to SQL 运算符
- **SelectMany**：允许将数组展开到 SQL JOIN 子句。 使用链接或嵌套表达式对数组元素进行筛选。
- **OrderBy**并**OrderByDescending**:转换为 ORDER BY 与 ASC 或 DESC。
- 用于聚合的 **Count**、**Sum**、**Min**、**Max** 和 **Average** 运算符及其异步等效项 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 和 **AverageAsync**。
- **CompareTo**：转换为范围比较。 通常用于字符串，因为它们不在.NET 中进行比较。
- **Take**：用于限制从查询结果转换为 SQL TOP。
- **数学函数**:支持从.NET 转换`Abs`， `Acos`， `Asin`， `Atan`， `Ceiling`， `Cos`， `Exp`， `Floor`， `Log`， `Log10`， `Pow`， `Round`， `Sign`， `Sin`， `Sqrt`， `Tan`，并`Truncate`为等效的 SQL 内置函数。
- **字符串函数**:支持从.NET 转换`Concat`， `Contains`， `Count`， `EndsWith`，`IndexOf`， `Replace`， `Reverse`， `StartsWith`， `SubString`， `ToLower`， `ToUpper`， `TrimEnd`，和`TrimStart`为等效的 SQL 内置函数。
- **数组函数**:支持从.NET 转换`Concat`， `Contains`，和`Count`为等效的 SQL 内置函数。
- **地理空间扩展函数**:支持从 stub 方法转换`Distance`， `IsValid`， `IsValidDetailed`，和`Within`为等效的 SQL 内置函数。
- **用户定义函数扩展函数**:支持从 stub 方法转换`UserDefinedFunctionProvider.Invoke`与对应的用户定义函数。
- **其他**：支持的转换`Coalesce`与条件运算符。 可以将翻译`Contains`为字符串 CONTAINS、 ARRAY_CONTAINS 或 SQL IN，具体取决于上下文。

### <a name="sql-query-operators"></a>SQL 查询运算符

以下示例说明了如何将一些标准 LINQ 查询运算符转换为 Cosmos DB 查询。

#### <a name="select-operator"></a>选择运算符

语法为 `input.Select(x => f(x))`，其中 `f` 是一个标量表达式。

**选择运算符，示例 1:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**选择运算符，示例 2:** 

- **LINQ lambda 表达式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**选择运算符，示例 3:**

- **LINQ lambda 表达式**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>SelectMany 运算符

语法为 `input.SelectMany(x => f(x))`，其中 `f` 是返回容器类型的标量表达式。

- **LINQ lambda 表达式**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Where 运算符

语法为 `input.Where(x => f(x))`，其中 `f` 是返回布尔值的标量表达式。

**其中运算符，示例 1:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**其中运算符，示例 2:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>复合 SQL 查询

可以编写前面的运算符，以形成更功能强大的查询。 由于 Cosmos DB 支持嵌套的容器，因此可以连接或嵌套组合。

#### <a name="concatenation"></a>串联

语法为 `input(.|.SelectMany())(.Select()|.Where())*`。 串联的查询可以使用的可选启动`SelectMany`查询后, 接多个`Select`或`Where`运算符。

**串联，示例 1:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**串联，示例 2:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**串联示例 3:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**串联示例 4:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>嵌套

语法是`input.SelectMany(x=>x.Q())`其中`Q`是`Select`， `SelectMany`，或`Where`运算符。

嵌套的查询适用于外部容器的每个元素的内部查询。 一个重要特点是，内部查询可以引用外部容器，如自联接中的元素的字段。

**嵌套，示例 1:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**嵌套，示例 2:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**嵌套，示例 3:**

- **LINQ lambda 表达式**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>参考

- [Azure Cosmos DB SQL 规范](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Javascript 规范](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe，Goetz。 [查询评估技术，对于大型数据库](https://dl.acm.org/citation.cfm?id=152611)。 *ACM 计算调查*25，没有。 2 (1993).
- Graefe，g。"级联查询优化的框架。" *IEEE Data Eng. L l。* 18、 没有。 3 (1995).
- Lu，Ooi，Tan。 "并行关系数据库系统中的处理查询"。 *IEEE 计算机协会出版社*(1994)。
- Olston、 Christopher、 Benjamin Reed、 Utkarsh Srivastava、 Ravi Kumar 和 Andrew Tomkins。 "Pig Latin:数据处理不因此外语言。" *SIGMOD* (2008)。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介][introduction]
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 一致性级别][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
