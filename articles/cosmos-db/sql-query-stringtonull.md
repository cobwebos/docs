---
title: Azure Cosmos DB 查询语言中的 StringToNull
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 StringToNull。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296433"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull （Azure Cosmos DB）
 返回转换为 null 的表达式。 如果 expression 无法转换，则返回 undefined。  
  
## <a name="syntax"></a>语法
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要分析为 null 表达式的字符串表达式。
  
## <a name="return-types"></a>返回类型
  
  返回空表达式或未定义。  
  
## <a name="examples"></a>示例
  
  下面的示例演示了 `StringToNull` 跨不同类型的行为。 

下面是有效输入的示例。

 仅允许在 "null" 之前或之后使用空格。

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 下面是结果集：  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

下面是无效输入的示例。

Null 区分大小写，并且必须用全部小写字符（即 "null"）来写入。

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 下面是结果集：  
  
```json
[{}]
```  

传递的表达式将被分析为 null 表达式;这些输入的计算结果不为类型 null，因此返回 undefined。

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
