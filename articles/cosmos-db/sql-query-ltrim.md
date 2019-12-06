---
title: Azure Cosmos DB 查询语言中的 LTRIM
description: 了解 Azure Cosmos DB 中的 LTRIM SQL 系统函数在删除前导空格后返回字符串表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 652204416c201ccca024aff0239fc10dcc3eb105
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870983"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM （Azure Cosmos DB）
 返回删除前导空格后的字符串表达式。  
  
## <a name="syntax"></a>语法
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `LTRIM`。  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 结果集如下。  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
