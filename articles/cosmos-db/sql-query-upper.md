---
title: Azure Cosmos DB 查询语言中的上限
description: 了解 Azure Cosmos DB 中的 SQL 系统函数上限。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303964"
---
# <a name="upper-azure-cosmos-db"></a>上部（Azure Cosmos DB）
 返回在将小写字符数据转换为大写后的字符串表达式。  

上部系统函数不使用索引。 如果您计划进行频繁的不区分大小写的比较，则系统函数可能会消耗大量 RU。 如果是这种情况，则可以在插入时规范化大小写，而不是使用大写系统函数来规范化数据以进行比较。 然后，如 SELECT * FROM c 的查询（其中 UPPER （c. name） = ' BOB '）只是 SELECT * FROM c，其中 c.name = ' BOB '。

## <a name="syntax"></a>语法
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `UPPER`  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 下面是结果集：  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
