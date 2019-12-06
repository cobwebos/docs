---
title: Azure Cosmos DB 查询语言中的 COS
description: 了解 Azure Cosmos DB 中的余弦（COS） SQL 系统函数如何返回指定表达式中以弧度表示的指定角度的三角余弦值。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873397"
---
# <a name="cos-azure-cosmos-db"></a>COS （Azure Cosmos DB）
 返回指定表达式中指定角度的三角余弦（弧度）。  
  
## <a name="syntax"></a>语法
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例计算指定角度的 `COS`。  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 结果集如下。  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
