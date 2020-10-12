---
title: Azure Cosmos DB 查询语言中的 REPLICATE
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 REPLICATE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88794324"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE (Azure Cosmos DB)
 将一个字符串值重复指定的次数。
  
## <a name="syntax"></a>语法
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   是一个字符串表达式。
  
*num_expr*  
   是一个数值表达式。 如果 *num_expr* 为负或非有限，则结果未定义。
  
## <a name="return-types"></a>返回类型
  
  返回一个字符串表达式。
  
## <a name="remarks"></a>备注

  结果的最大长度为 10,000 个字符，即 (length(*str_expr*)  *  *num_expr*) <= 10,000。 此系统函数不会使用索引。

## <a name="examples"></a>示例
  
  以下示例演示如何在查询中使用 `REPLICATE`。
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 下面是结果集。
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
