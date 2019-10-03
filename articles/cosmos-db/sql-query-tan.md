---
title: Azure Cosmos DB 查询语言中 TAN
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 TAN。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349158"
---
# <a name="tan-azure-cosmos-db"></a>TAN （Azure Cosmos DB）
 返回指定表达式中指定角度（弧度）的正切值。  
  
## <a name="syntax"></a>语法
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例可计算 PI()/2 的正切值。  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 结果集如下。  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
