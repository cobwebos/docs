---
title: 反向 Azure Cosmos DB 查询语言
description: 了解 Azure Cosmos DB 中的 SQL 系统函数逆向。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302162"
---
# <a name="reverse-azure-cosmos-db"></a>反向（Azure Cosmos DB）
 返回字符串值的逆序。  
  
## <a name="syntax"></a>语法
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `REVERSE`。  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 下面是结果集：  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
