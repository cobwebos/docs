---
title: Azure Cosmos DB 查询语言中的 ENDSWITH
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 ENDSWITH。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c988d63e597c77bc09a1d21ad391909bb55901
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351050"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH （Azure Cosmos DB）
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。  
  
## <a name="syntax"></a>语法
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   字符串表达式。  
  
*str_expr2*  
   要与*str_expr1*的结尾进行比较的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回以 "b" 和 "bc" 结尾的 "abc"。  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 结果集如下。  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
