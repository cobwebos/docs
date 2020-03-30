---
title: Azure Cosmos DB 查询语言中的 SUBSTRING
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 SUBSTRING。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303692"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 返回字符串表达式的部分内容，该内容起于指定字符从零开始的位置，继续到指定长度或字符串结尾。  
  
## <a name="syntax"></a>语法
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>自变量
  
*str_expr*  
   是一个字符串表达式。
  
*num_expr1*  
   是表示开始字符的数字表达式。 值 0 是 *str_expr* 的第一个字符。
  
*num_expr2*  
   是一个数值表达式，表示要返回的 *str_expr* 的最大字符数。 值为 0 或更小将导致空字符串。

## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回 "abc" 中从位置 1 开始且长度为 1 个字符的子字符串。  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 下面是结果集：  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>备注

如果起始位置为`0`，则此系统功能将受益于[范围索引](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 简介](introduction.md)
