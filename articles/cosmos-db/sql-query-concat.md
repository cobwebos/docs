---
title: Azure Cosmos DB 查询语言中的 CONCAT
description: 了解 Azure Cosmos DB 中的 CONCAT SQL 系统函数如何返回一个字符串，该字符串是连接两个或更多字符串值的结果
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302604"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT （Azure Cosmos DB）
 返回作为串联两个或更多字符串值的结果的字符串。  
  
## <a name="syntax"></a>语法
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要连接到其他值的字符串表达式。 `CONCAT` 函数需要至少两个*str_expr*参数。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回指定值的串联字符串。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 下面是结果集：  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
