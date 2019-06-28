---
title: Azure Cosmos DB SQL 查询中的标量表达式
description: 了解有关 Azure Cosmos DB 的标量表达式的 SQL 语法。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342581"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Azure Cosmos DB SQL 查询中的标量表达式

[SELECT 子句](sql-query-select.md)支持标量表达式。 标量表达式是符号和运算符的组合，经计算后可获得单个值。 标量表达式的示例包括： 常量、 属性引用、 数组元素引用、 别名引用或函数调用。 标量表达式可以合并到复杂的表达式使用运算符。

## <a name="syntax"></a>语法
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>参数
  
- `<constant>`  
  
   表示一个常数值。 有关详细信息，请参阅[常数](sql-query-constants.md)部分。  
  
- `input_alias`  
  
   表示由 `FROM` 子句引入，由 `input_alias` 定义的值。  
  该值不得是“未定义”  –输入中的“未定义”值将被跳过  。  
  
- `<scalar_expression>.property_name`  
  
   表示对象的属性值。 如果不存在此属性或属性引用上一个值，该值不是一个对象，则表达式的计算结果**未定义**值。  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   表示具有名称的属性的值`property_name`或具有索引的数组元素`array_index`的数组。 如果不存在属性/数组索引，或对非对象/数组的值引用了属性/数组索引，则表达式的求值结果为未定义值。  
  
- `unary_operator <scalar_expression>`  
  
   表示应用于单个值的运算符。 有关详细信息，请参阅[运算符](sql-query-operators.md)部分。  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   表示应用于两个值的运算符。 有关详细信息，请参阅[运算符](sql-query-operators.md)部分。  
  
- `<scalar_function_expression>`  
  
   表示由函数调用结果定义的值。  
  
- `udf_scalar_function`  
  
   用户定义的标量函数的名称。  
  
- `builtin_scalar_function`  
  
   内置标量函数的名称。  
  
- `<create_object_expression>`  
  
   表示通过使用指定属性及其值创建一个新对象来获得的值。  
  
- `<create_array_expression>`  
  
   表示通过使用指定值作为元素创建一个新数组来获得的值。  
  
- `parameter_name`  
  
   表示指定参数名称的值。 参数名称必须以单个 \@ 作为第一个字符。  
  
## <a name="remarks"></a>备注
  
  调用内置或用户定义的标量函数时，必须定义所有参数。 如果未定义任何参数，则不会调用该函数，且结果也将为未定义。  
  
  创建对象时，将跳过任何已分配未定义值的属性，并且在创建的对象中不包含该属性。  
  
  创建数组时，将跳过任何已分配“未定义”值  的元素值，并且在创建的对象中不包含该值。 这将导致下一个定义的元素取代该值时，创建的数组将不含跳过的索引。  

## <a name="examples"></a>示例

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

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmosdb-dotnet)
- [子查询](sql-query-subquery.md)