---
title: Azure Cosmos DB 查询语言的长度
description: 了解 Azure Cosmos DB 中的 SQL 系统函数长度。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349748"
---
# <a name="length-azure-cosmos-db"></a>长度（Azure Cosmos DB）
 返回指定字符串表达式的字符数。  
  
## <a name="syntax"></a>语法
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   要计算的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回字符串的长度。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 结果集如下。  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
