---
title: Azure Cosmos DB 查询语言中的 CEILING
description: 了解 Azure Cosmos DB 中的 CEILING SQL 系统函数如何返回大于或等于指定数值表达式的最小整数值。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302638"
---
# <a name="ceiling-azure-cosmos-db"></a>CEILING (Azure Cosmos DB)
 返回大于或等于指定数值表达式的最小整数值。  
  
## <a name="syntax"></a>语法
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例显示了如何对正值、负值和零值使用 `CEILING` 函数。  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 下面是结果集。  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
