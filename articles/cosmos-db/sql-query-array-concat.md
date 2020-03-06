---
title: Azure Cosmos DB 查询语言中的 ARRAY_CONCAT
description: 了解 Azure Cosmos DB 中的数组串联 SQL 系统函数如何返回数组，该数组是连接两个或更多数组值的结果
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78295872"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT （Azure Cosmos DB）
 返回一个数组，该数组是连接两个或更多数组值的结果。  
  
## <a name="syntax"></a>语法
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>参数
  
*arr_expr*  
   用于连接到其他值的数组表达式。 `ARRAY_CONCAT` 函数需要至少两个*arr_expr*参数。  
  
## <a name="return-types"></a>返回类型
  
  返回数组表达式。  
  
## <a name="examples"></a>示例
  
  以下示例介绍了如何连接两个数组。  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 下面是结果集：  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数组函数 Azure Cosmos DB](sql-query-array-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
