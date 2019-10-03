---
title: Azure Cosmos DB 查询语言中的 RTRIM
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RTRIM。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 511c085b465b8b70ae71c298054bcb535773837e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349509"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM （Azure Cosmos DB）
 在删除尾随空格后，返回字符串表达式。  
  
## <a name="syntax"></a>语法
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   为任何有效的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `RTRIM`。  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 结果集如下。  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
