---
title: 在 Azure Cosmos DB 查询语言中替换
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 REPLACE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349583"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE （Azure Cosmos DB）
 将出现的所有指定字符串值替换为另一个字符串值。  
  
## <a name="syntax"></a>语法
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   要搜索的字符串表达式。  
  
*str_expr2*  
   要查找的字符串表达式。  
  
*str_expr3*  
   替换*str_expr1*中的*str_expr2*的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 @no__t 0。  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 结果集如下。  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
