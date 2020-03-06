---
title: Azure Cosmos DB 查询语言中的 RTRIM
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RTRIM。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b740d14315f6d9ba2f1788c56d6b1fcd8945c83e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302077"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM （Azure Cosmos DB）
 在删除尾随空格后，返回字符串表达式。  
  
## <a name="syntax"></a>语法
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   为任何有效的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `RTRIM`。  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 下面是结果集：  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
