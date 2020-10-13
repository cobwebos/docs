---
title: Azure Cosmos DB 中的 LINQ to SQL 转换
description: 了解支持的 LINQ 运算符，以及如何将 LINQ 查询映射到 Azure Cosmos DB 中的 SQL 查询。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 7/29/2020
ms.author: tisande
ms.openlocfilehash: f2a7570b7ebed26a06e1bd075c2904bc29061c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498848"
---
# <a name="linq-to-sql-translation"></a>LINQ 到 SQL 转换

Azure Cosmos DB 查询提供程序执行从 LINQ 查询到 Cosmos DB SQL 查询的最有效映射。 如果要获取从 LINQ 转换的 SQL 查询，请对生成的 `IQueryable` 对象使用 `ToString()` 方法。 以下内容假设你对 [LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) 有一个基本的了解。

查询提供程序类型系统仅支持 JSON 基元类型：数字、布尔值、字符串和 null。

查询提供程序支持以下标量表达式：

- 常量值，包括评估查询时基元数据类型的常量值。
  
- 引用对象或数组元素的属性的属性/数组索引表达式。 例如：
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算术表达式，包括针对数值和布尔值运行的常见算术表达式。 有关完整列表，请参阅 [Azure Cosmos DB SQL 规范](sql-query-system-functions.md)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字符串比较表达式，包括将字符串值与某些常量字符串值进行比较。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 对象/数组创建表达式，返回复合值类型或匿名类型的对象，或此类对象组成的数组。 可以嵌套这些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>使用 LINQ

可以使用 `GetItemLinqQueryable` 创建 LINQ 查询。 此示例演示如何使用 `FeedIterator` 生成 LINQ 查询并进行异步执行：

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>支持的 LINQ 运算符

SQL .NET SDK 随附的 LINQ 提供程序支持以下运算符：

- **Select**：投影转换为 [SELECT](sql-query-select.md)（包括对象构造）。
- **Where**：筛选器转换为 [WHERE](sql-query-where.md)，并且支持 `&&`、`||` 和 `!` 到 SQL 运算符的转换
- **SelectMany**：允许将数组展开到 [JOIN](sql-query-join.md) 子句。 用于将表达式链接或嵌套到对数组元素应用的筛选器。
- **OrderBy** 和 **OrderByDescending**：使用 ASC 或 DESC 转换为 [ORDER BY](sql-query-order-by.md)。
-           用于[聚合](sql-query-aggregates.md)的 Count、Sum、Min、Max 和 Average 运算符及其异步等效项 CountAsync、SumAsync、MinAsync、MaxAsync 和 AverageAsync。
- **CompareTo**：转换为范围比较。 通常用于字符串，因为它们在 .NET 中不可进行比较。
- **Skip** 和 **Take**：转换为 [OFFSET 和 LIMIT](sql-query-offset-limit.md)，用于限制查询的结果和进行分页。
- **数学函数**：支持从 .NET `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan` 和 `Truncate` 转换为等效的[内置数学函数](sql-query-mathematical-functions.md)。
- **字符串函数**：支持从 .NET `Concat`、`Contains`、`Count`、`EndsWith`、`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd` 和 `TrimStart` 转换为等效的[内置字符串函数](sql-query-string-functions.md)。
- **数组函数**：支持从 .NET `Concat`、`Contains` 和 `Count` 转换为等效的[内置数组函数](sql-query-array-functions.md)。
- **地理空间扩展函数**：支持从存根方法 `Distance`、`IsValid`、`IsValidDetailed` 和 `Within` 转换为等效的[内置地理空间函数](sql-query-geospatial-query.md)。
- **用户定义的函数扩展函数**：支持从存根方法 `UserDefinedFunctionProvider.Invoke` 转换为相应的[用户定义的函数](sql-query-udfs.md)。
- **其他**：支持 `Coalesce` 和条件[运算符](sql-query-operators.md)的转换。 可以根据上下文将 `Contains` 转换为字符串 CONTAINS、ARRAY_CONTAINS 或 IN。

## <a name="examples"></a>示例

以下示例演示了一些标准 LINQ 查询运算符如何转换为 Azure Cosmos DB 中的查询。

### <a name="select-operator"></a>Select 运算符

语法为 `input.Select(x => f(x))`，其中 `f` 是一个标量表达式。 这种情况下，`input` 将为 `IQueryable` 对象。

**Select 运算符，示例 1：**

- **LINQ Lambda 表达式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 运算符，示例 2：**

- **LINQ Lambda 表达式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 运算符，示例 3：**

- **LINQ Lambda 表达式**
  
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

### <a name="selectmany-operator"></a>SelectMany 运算符

语法为 `input.SelectMany(x => f(x))`，其中 `f` 是返回容器类型的标量表达式。

- **LINQ Lambda 表达式**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where 运算符

语法为 `input.Where(x => f(x))`，其中 `f` 是返回布尔值的标量表达式。

**Where 运算符，示例 1：**

- **LINQ Lambda 表达式**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 运算符，示例 2：**

- **LINQ Lambda 表达式**
  
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

## <a name="composite-sql-queries"></a>复合 SQL 查询

将上述运算符组合到一起可以构成更强大的查询。 由于 Cosmos DB 支持嵌套的容器，因此你可以连接或嵌套这种组合。

### <a name="concatenation"></a>串联

语法为 `input(.|.SelectMany())(.Select()|.Where())*`。 连接的查询可以使用可选的 `SelectMany` 查询开头，后接多个 `Select` 或 `Where` 运算符。

**连接，示例 1：**

- **LINQ Lambda 表达式**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**连接，示例 2：**

- **LINQ Lambda 表达式**
  
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

**连接，示例 3：**

- **LINQ Lambda 表达式**
  
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

**连接，示例 4：**

- **LINQ Lambda 表达式**
  
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

### <a name="nesting"></a>嵌套

语法为 `input.SelectMany(x=>x.Q())`，其中 `Q` 是 `Select`、`SelectMany` 或 `Where` 运算符。

嵌套查询会将内部查询应用到外部容器的每个元素。 一个重要的功能是内部查询可以引用外部容器（如自联接）中元素的字段。

**嵌套，示例 1：**

- **LINQ Lambda 表达式**
  
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

**嵌套，示例 2：**

- **LINQ Lambda 表达式**
  
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

**嵌套，示例 3：**

- **LINQ Lambda 表达式**
  
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

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文档数据](modeling-data.md)
