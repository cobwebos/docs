---
title: Azure Cosmos DB 查询语言中的 LOG10
description: 了解 Azure Cosmos DB 中的 LOG10 SQL 系统函数以返回指定数值表达式以10为底的对数
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302485"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 （Azure Cosmos DB）
 返回指定数值表达式中以 10 为底数的对数。  
  
## <a name="syntax"></a>语法
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expression*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="remarks"></a>备注
  
  LOG10 和 POWER 函数彼此反向相关。 例如，10 ^ LOG10(n) = n。  
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回到指定变量 (100) 的 LOG10 值。  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 下面是结果集：  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
