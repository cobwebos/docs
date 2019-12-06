---
title: Azure Cosmos DB 查询语言中的 LOG10
description: 了解 Azure Cosmos DB 中的 LOG10 SQL 系统函数以返回指定数值表达式以10为底的对数
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5950a2a40a78716a76e49fcdaeb3129948b6a0e1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871051"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 （Azure Cosmos DB）
 返回指定数值表达式中以 10 为底数的对数。  
  
## <a name="syntax"></a>语法
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expression*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="remarks"></a>备注
  
  LOG10 和 POWER 函数互为反函数。 例如，10 ^ LOG10(n) = n。  
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回到指定变量 (100) 的 LOG10 值。  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 结果集如下。  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
