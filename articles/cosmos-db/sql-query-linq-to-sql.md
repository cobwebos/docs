---
title: Azure Cosmos DB 中的 LINQ to SQL 翻译
description: 了解支持的 LINQ 运算符，以及如何将 LINQ 查询映射到 Azure Cosmos DB 中的 SQL 查询。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d8dd6392cf22852a10c1dc2600edcbc647f3c510
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871153"
---
# <a name="linq-to-sql-translation"></a>LINQ 到 SQL 转换

Azure Cosmos DB 查询提供程序执行从 LINQ 查询到 Cosmos DB SQL 查询的最大工作量映射。 以下说明假定你基本熟悉 LINQ。

查询提供程序类型系统仅支持 JSON 基元类型：数字、布尔、字符串和 null。

查询提供程序支持以下标量表达式：

- 常数值，包括查询计算时基元数据类型的常量值。
  
- 引用对象或数组元素的属性的属性/数组索引表达式。 例如：
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算术表达式，包括数值和布尔值上的常用算术表达式。 有关完整列表，请参阅[AZURE COSMOS DB SQL 规范](https://go.microsoft.com/fwlink/p/?LinkID=510612)。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 字符串比较表达式，其中包括将字符串值与某些常量字符串值进行比较。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 对象/数组创建表达式，该表达式返回复合值类型或匿名类型的对象或此类对象的数组。 可以嵌套这些值。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>支持的 LINQ 运算符

SQL .NET SDK 随附的 LINQ 提供程序支持以下运算符：

- **Select**：投影转换为 SQL Select （包括对象构造）。
- **Where**：筛选器转换为 sql Where，并支持 `&&`、`||`和 `!` 之间的转换到 sql 运算符
- **SelectMany**：允许将数组展开到 SQL JOIN 子句。 用于链接或嵌套表达式以对数组元素进行筛选。
- **OrderBy**和**OrderByDescending**：转换为 ORDER BY with ASC 或 DESC。
- 用于聚合的 **Count**、**Sum**、**Min**、**Max** 和 **Average** 运算符及其异步等效项 **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync** 和 **AverageAsync**。
- **CompareTo**：转换为范围比较。 通常用于字符串，因为它们在 .NET 中不可比较。
- **Take**：转换为 SQL TOP，用于限制查询中的结果。
- **数学函数**：支持从 .net `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan`和 `Truncate` 转换为等效的 SQL 内置函数。
- **字符串函数**：支持从 .net `Concat`、`Contains`、`Count`、`EndsWith`、`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd`和 `TrimStart` 转换为等效的 SQL 内置函数。
- **数组函数**：支持从 .net `Concat`、`Contains`和 `Count` 转换为等效的 SQL 内置函数。
- **地理空间扩展函数**：支持从存根方法 `Distance`、`IsValid`、`IsValidDetailed`和 `Within` 转换为等效的 SQL 内置函数。
- **用户定义的函数扩展函数**：支持从存根方法 `UserDefinedFunctionProvider.Invoke` 转换为相应的用户定义函数。
- **其他**：支持 `Coalesce` 和条件运算符的转换。 可以将 `Contains` 转换为中的字符串包含、ARRAY_CONTAINS 或 SQL，具体取决于上下文。

## <a name="examples"></a>示例

下面的示例演示了一些标准 LINQ 查询运算符如何转换为 Cosmos DB 查询。

### <a name="select-operator"></a>选择运算符

语法为 `input.Select(x => f(x))`，其中 `f` 是一个标量表达式。

**选择运算符，示例1：**

- **LINQ Lambda 表达式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**选择运算符，示例2：** 

- **LINQ Lambda 表达式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 运算符，示例3：**

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

**Where 运算符，示例1：**

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
  
**Where 运算符，示例2：**

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

您可以编写上述运算符来形成功能更强大的查询。 由于 Cosmos DB 支持嵌套容器，因此可以连接或嵌套组合。

### <a name="concatenation"></a>串联

语法为 `input(.|.SelectMany())(.Select()|.Where())*`。 串联的查询可以从一个可选的 `SelectMany` 查询开始，后跟多个 `Select` 或 `Where` 运算符。

**串联，示例1：**

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

**串联，示例2：**

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

**串联，示例3：**

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

**串联，示例4：**

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

语法为 `input.SelectMany(x=>x.Q())`，其中 `Q` 是 `Select`、`SelectMany`或 `Where` 运算符。

嵌套查询将内部查询应用到外部容器的每个元素。 一项重要的功能是内部查询可以引用外部容器中的元素字段，如自联接。

**嵌套，示例1：**

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

**嵌套，示例2：**

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

**嵌套，示例3：**

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

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文档数据](modeling-data.md)
