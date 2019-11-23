---
title: Azure Cosmos DB 查询语言中的 REVERSE
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 REVERSE。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8237918232bd8ba8edb2b8f71440ffd73a913334
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349560"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
 返回字符串值的逆序排序形式。  
  
## <a name="syntax"></a>语法
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   是一个字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例演示如何在查询中使用 `REVERSE`。  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 结果集如下。  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
