---
title: Azure Cosmos DB 查询语言中的 StringToBoolean
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToBoolean。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296535"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean （Azure Cosmos DB）
 返回转换为布尔值的表达式。 如果 expression 无法转换，则返回 undefined。  
  
## <a name="syntax"></a>语法
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要分析为布尔表达式的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回布尔表达式或未定义。  
  
## <a name="examples"></a>示例
  
  下面的示例演示了 `StringToBoolean` 跨不同类型的行为。 
 
 下面是有效输入的示例。

只有 "true"/"false" 前后才允许使用空格。

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 下面是结果集：  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

下面是无效输入的示例。

 布尔值区分大小写，并且必须用全部小写字符（即 "true" 和 "false"）来编写。

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

下面是结果集：  
  
```json
[{}]
``` 

传递的表达式将被分析为布尔表达式;这些输入的计算结果不是布尔值，因此返回 undefined。

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

下面是结果集：  
  
```json
[{}]
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
