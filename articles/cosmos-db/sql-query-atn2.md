---
title: Azure Cosmos DB 查询语言中的 ATN2
description: 了解 Azure Cosmos DB 中的 ATN2 SQL 系统函数如何返回 y/x 的反正切值（以弧度表示）
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11321ef9d7b81af279b04e0e435b19c645cf3bcf
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871646"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 （Azure Cosmos DB）
 返回 y/x 的反正切函数的主值（以弧度表示）。  
  
## <a name="syntax"></a>语法
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   为数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例可计算指定的 x 分量和 y 分量的 ATN2 值。  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 结果集如下。  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
