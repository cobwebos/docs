---
title: Azure Cosmos DB 查询语言中的 ARRAY_CONCAT
description: 了解 Azure Cosmos DB 中的数组串联 SQL 系统函数如何返回数组，该数组是连接两个或更多数组值的结果
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871816"
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
  
 结果集如下。  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>后续步骤

- [数组函数 Azure Cosmos DB](sql-query-array-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
