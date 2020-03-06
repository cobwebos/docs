---
title: 以 Azure Cosmos DB 查询语言进行复制
description: 了解 Azure Cosmos DB 中的 SQL 系统函数复制。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302179"
---
# <a name="replicate-azure-cosmos-db"></a>复制（Azure Cosmos DB）
 以指定的次数重复字符串值。
  
## <a name="syntax"></a>语法
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。
  
*num_expr*  
   是一个数值表达式。 如果*num_expr*为负数或非有限，则结果是不确定的。
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。
  
## <a name="remarks"></a>备注
  结果的最大长度为10000个字符，即（长度（*str_expr*） * *num_expr*） < = 10000。

## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `REPLICATE`。
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 下面是结果集：
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
