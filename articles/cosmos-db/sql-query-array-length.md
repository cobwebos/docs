---
title: Azure Cosmos DB 查询语言中的 ARRAY_LENGTH
description: 了解 Azure Cosmos DB 中的数组长度 SQL 系统函数如何返回指定数组表达式的元素数
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9a8bf33befdd842a2979151fef3d54679ee03de1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871765"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH （Azure Cosmos DB）
 返回指定数组表达式的元素数。  
  
## <a name="syntax"></a>语法
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*arr_expr*  
   是数组表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何使用 `ARRAY_LENGTH`获取数组的长度。  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 结果集如下。  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>后续步骤

- [数组函数 Azure Cosmos DB](sql-query-array-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
