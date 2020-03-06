---
title: Azure Cosmos DB 查询语言中的子字符串
description: 了解 Azure Cosmos DB 中的 SQL 系统函数子字符串。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303692"
---
# <a name="substring-azure-cosmos-db"></a>子字符串（Azure Cosmos DB）
 返回字符串表达式的部分内容，该内容起于指定字符从零开始的位置，继续到指定长度或字符串结尾。  
  
## <a name="syntax"></a>语法
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。
  
*num_expr1*  
   表示起始字符的数值表达式。 值0是*str_expr*的第一个字符。
  
*num_expr2*  
   一个数值表达式，表示要返回的*str_expr*的最大字符数。 如果值为0或更小，则结果为空字符串。

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

如果开始位置 `0`，则此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中受益。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
