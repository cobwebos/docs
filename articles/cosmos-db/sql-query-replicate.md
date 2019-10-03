---
title: 以 Azure Cosmos DB 查询语言进行复制
description: 了解 Azure Cosmos DB 中的 SQL 系统函数复制。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349571"
---
# <a name="replicate-azure-cosmos-db"></a>复制（Azure Cosmos DB）
 将一个字符串值重复指定的次数。
  
## <a name="syntax"></a>语法
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。
  
*num_expr*  
   为数值表达式。 如果*num_expr*为负或非有限，则结果是不确定的。
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。
  
## <a name="remarks"></a>备注
  结果的最大长度为10000个字符，即（长度（*str_expr*） * *num_expr*） < = 10000。

## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 @no__t 0。
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 结果集如下。
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
