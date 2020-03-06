---
title: Azure Cosmos DB 查询语言中的 ACOS
description: 了解 Azure Cosmos DB 中的 ACOS （arccosice） SQL 系统函数如何返回以弧度表示的角度，其余弦为指定数值表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300955"
---
# <a name="acos-azure-cosmos-db"></a>ACOS （Azure Cosmos DB）
 返回角度（弧度），其余弦是指定的数值表达式；也被称为反余弦。  
  
## <a name="syntax"></a>语法
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例返回-1 的 `ACOS`。  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 下面是结果集：  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
