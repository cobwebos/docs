---
title: Azure Cosmos DB 查询语言中保留
description: 了解 Azure Cosmos DB 中的 SQL 系统函数。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2e175e1ed62a4afb2a532add161dd2ab63ba9b1c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349775"
---
# <a name="left-azure-cosmos-db"></a>LEFT （Azure Cosmos DB）
 返回具有指定字符数的字符串的左侧部分。  
  
## <a name="syntax"></a>语法
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要从中提取字符的字符串表达式。  
  
*num_expr*  
   指定字符数的数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回不同长度值的 "abc" 的左侧部分。  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 结果集如下。  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
