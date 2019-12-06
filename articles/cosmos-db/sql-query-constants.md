---
title: Azure Cosmos DB 中的 SQL 常量
description: 了解如何使用 Azure Cosmos DB 中的 SQL 查询常量来表示特定数据值
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873414"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL 查询常量  

 常数也称为文本值或标量值，是表示特定数据值的符号。 常数的格式取决于它代表的值的数据类型。  
  
 受支持的标量数据类型：  
  
|类型|值顺序|  
|-|-|  
|未定义|单个值：未定义|  
|Null|单个值：null|  
|**布尔值**|值："false"、"true"。|  
|**Number**|双精度浮点数，IEEE 754 标准。|  
|**字符串**|零个或多个 Unicode 字符序列。 字符串必须用单引号或双引号括起来。|  
|数组|零个或多个元素序列。 每个元素可以是任何标量数据类型的值，但**Undefined**除外。|  
|**Object**|零个或多个名称/值对的无序集。 当名称为一个Unicode字符串，值可以属于任何标量数据类型，“未定义”除外。|  
  
## <a name="bk_syntax"></a>语法
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> 参数
  
* `<undefined_constant>; Undefined`  
  
  表示 Undefined 类型的未定义值。  
  
* `<null_constant>; null`  
  
  表示 Null 类型的 null 值。  
  
* `<boolean_constant>`  
  
  表示 Boolean 类型的常数。  
  
* `false`  
  
  表示 Boolean 类型的 false 值。  
  
* `true`  
  
  表示 Boolean 类型的 true 值。  
  
* `<number_constant>`  
  
  表示常数。  
  
* `decimal_literal`  
  
  十进制文本是使用十进制表示法或科学记数法表示的数字。  
  
* `hexadecimal_literal`  
  
  十六进制文本是使用 "0x" 前缀表示的数字，后跟一个或多个十六进制数字。  
  
* `<string_constant>`  
  
  表示字符串类型的常数。  
  
* `string _literal`  
  
  字符串文本是以零个或多个 Unicode 字符序列或转义符序列表示的 Unicode 字符串。 字符串文本括在单引号 (') 或双引号 (") 中。  
  
  允许以下转义序列：  
  
|**转义序列**|**说明**|**Unicode 字符**|  
|-|-|-|  
|\\'|撇号 (')|U+0027|  
|\\"|引号 (")|U+0022|  
|\\\ |反斜线号 (\\)|U+005C|  
|\\/|斜线号 (/)|U+002F|  
|\b|退格符|U+0008|  
|\f|换页符|U+000C|  
|\n|换行符|U+000A|  
|\r|回车符|U+000D|  
|\t|Tab|U+0009|  
|\uXXXX|由 4 个十六进制数字定义的 Unicode 字符。|U+XXXX|  

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB.NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [模型文档数据](modeling-data.md)
