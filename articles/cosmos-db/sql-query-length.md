---
title: Azure Cosmos DB 查询语言的长度
description: 了解 Azure Cosmos DB 中的 SQL 系统函数长度。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303709"
---
# <a name="length-azure-cosmos-db"></a>长度（Azure Cosmos DB）
 返回指定字符串表达式的字符数。  
  
## <a name="syntax"></a>语法
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要计算的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回字符串的长度。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 下面是结果集：  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
