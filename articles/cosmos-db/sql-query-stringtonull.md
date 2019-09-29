---
title: Azure Cosmos DB 查询语言中的 StringToNull
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToNull。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c7ea3ca82cadb1351d8581f3d652ce060d67969b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349245"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull （Azure Cosmos DB）
 返回已转换为 Null 的表达式。 如果表达式无法转换，则返回未定义的表达式。  
  
## <a name="syntax"></a>语法
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要分析为 null 表达式的字符串表达式。
  
## <a name="return-types"></a>返回类型
  
  返回一个 Null 表达式或未定义的表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示在不同类型之间 @no__t 的行为。 

下面是输入有效的示例。

 只能在 "null" 之前或之后使用空格。

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 结果集如下。  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

下面是输入无效的示例。

Null 值区分大小写，必须全用小写字符（即 "null"）来表示。

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 结果集如下。  
  
```json
[{}]
```  

传递的表达式将会解析为 null 表达式；以下输入不会计算为 null 类型，因此会返回未定义。

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 结果集如下。  
  
```json
[{}]
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
