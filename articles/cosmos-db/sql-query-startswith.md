---
title: Azure Cosmos DB 查询语言中的 STARTSWITH
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 STARTSWITH。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349309"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH （Azure Cosmos DB）
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。  
  
## <a name="syntax"></a>语法
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   字符串表达式。
  
*str_expr2*  
   要与*str_expr1*的开头进行比较的字符串表达式。

## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  以下示例检查了字符串 "abc" 是否以 "b" 和 "a" 开头。  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 结果集如下。  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
