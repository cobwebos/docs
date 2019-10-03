---
title: Azure Cosmos DB 查询语言中的 SIN
description: 在 Azure Cosmos DB 中了解 SQL 系统函数的 SIN。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349459"
---
# <a name="sin-azure-cosmos-db"></a>SIN （Azure Cosmos DB）
 返回指定表达式中指定角度的三角正弦（弧度）。  
  
## <a name="syntax"></a>语法
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例计算指定角度的 @no__t 0。  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 结果集如下。  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
