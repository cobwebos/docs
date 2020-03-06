---
title: Azure Cosmos DB 查询语言中包含
description: 了解 Azure Cosmos DB 中包含的 SQL 系统函数如何返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302587"
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
  
 下面是结果集：  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
