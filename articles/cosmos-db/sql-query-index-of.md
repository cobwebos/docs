---
title: Azure Cosmos DB 查询语言中的 INDEX_OF
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 INDEX_OF。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350986"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF （Azure Cosmos DB）
 返回第一个指定的字符串表达式中第一次出现第二个字符串表达式的起始位置，如果未找到字符串，则返回 -1。  
  
## <a name="syntax"></a>语法
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   要搜索的字符串表达式。  
  
*str_expr2*  
   要搜索的字符串表达式。  

*numeric_expr*可选的数值表达式，用于设置搜索开始的位置。 *Str_expr1*中的第一个位置是0。 
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回 "abc" 内不同子字符串的索引。  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 结果集如下。  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
