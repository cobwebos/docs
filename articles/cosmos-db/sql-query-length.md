---
title: Azure Cosmos DB 查询语言中的 LENGTH
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 LENGTH。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303709"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
 返回指定字符串表达式的字符数。  
  
## <a name="syntax"></a>语法
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   是要计算的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例返回某个字符串的长度。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 下面是结果集。  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
