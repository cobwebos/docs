---
title: Azure Cosmos DB 查询语言中包含
description: 了解 Azure Cosmos DB 中的 SQL 系统函数包含。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5d5018d0173c316a0a31bd2f70e15e5ff972d153
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351088"
---
# <a name="contains-azure-cosmos-db"></a>CONTAINS （Azure Cosmos DB）
 返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。  
  
## <a name="syntax"></a>语法
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   要搜索的字符串表达式。  
  
*str_expr2*  
   要查找的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例检查 "abc" 是否包含 "ab" 和 "abc" 是否包含 "d"。  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 结果集如下。  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
