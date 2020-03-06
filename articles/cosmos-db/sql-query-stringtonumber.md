---
title: Azure Cosmos DB 查询语言中的 StringToNumber
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToNumber。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296416"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber （Azure Cosmos DB）
 返回转换为数字的表达式。 如果 expression 无法转换，则返回 undefined。  
  
## <a name="syntax"></a>语法
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要分析为 JSON 数字表达式的字符串表达式。 JSON 中的数字必须是整数或浮点。 有关 JSON 格式的详细信息，请参阅[json.org](https://json.org/)  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式或未定义。  
  
## <a name="examples"></a>示例
  
  下面的示例演示了 `StringToNumber` 跨不同类型的行为。 

仅允许在数字之前或之后使用空格。

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 下面是结果集：  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

在 JSON 中，有效数字必须是整数或浮点数。

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 下面是结果集：  
  
```json
{{}}
```  

传递的表达式将被分析为数字表达式;这些输入的计算结果不是类型 Number，因而返回 undefined。 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 下面是结果集：  
  
```json
{{}}
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
