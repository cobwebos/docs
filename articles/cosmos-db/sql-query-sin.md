---
title: Azure Cosmos DB 查询语言中的 SIN
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 SIN。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303097"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
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
  
  以下示例计算指定角度的 `SIN`。  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 下面是结果集：  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
