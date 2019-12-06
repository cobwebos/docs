---
title: Azure Cosmos DB 查询语言中的 COT
description: 了解 Azure Cosmos DB 中的余切（COT） SQL 系统函数如何返回指定数值表达式中以弧度表示的指定角度的三角余切
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871493"
---
# <a name="cot-azure-cosmos-db"></a>COT （Azure Cosmos DB）
 返回指定数值表达式中指定角度的三角余切。  
  
## <a name="syntax"></a>语法
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例计算指定角度的 `COT`。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 结果集如下。  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
