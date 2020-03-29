---
title: Azure Cosmos DB 查询语言中的 RIGHT
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RIGHT。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302145"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
 返回具有指定字符数的字符串的右侧部分。  
  
## <a name="syntax"></a>语法
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>自变量
  
*str_expr*  
   要从中提取字符的字符串表达式。  
  
*num_expr*  
   是指定字符数的数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回不同长度值的 "abc" 的右侧部分。  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 下面是结果集：  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>备注

此系统功能不会利用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 简介](introduction.md)
