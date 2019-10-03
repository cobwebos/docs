---
title: Azure Cosmos DB 查询语言的 POWER
description: 了解 Azure Cosmos DB 中的 SQL 系统函数电源。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349634"
---
# <a name="power-azure-cosmos-db"></a>电源（Azure Cosmos DB）
 返回指定表达式相对指定幂的值。  
  
## <a name="syntax"></a>语法
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr1*  
   为数值表达式。  
  
*numeric_expr2*  
   是*numeric_expr1*的幂。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例演示了某个数自乘 3 次的幂（该数的立方）。  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 结果集如下。  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
