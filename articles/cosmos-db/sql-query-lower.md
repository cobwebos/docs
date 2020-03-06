---
title: Azure Cosmos DB 查询语言中较低
description: 了解 Azure Cosmos DB 中的较低 SQL 系统函数在将大写字符数据转换为小写后返回字符串表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302264"
---
# <a name="lower-azure-cosmos-db"></a>LOWER （Azure Cosmos DB）
 返回在将大写字符数据转换为小写后的字符串表达式。  

低系统函数不使用索引。 如果打算执行频繁的不区分大小写的比较，则系统函数越小，可能会消耗大量 RU。 如果是这种情况，则可以在插入时规范化大小写，而不是使用低系统函数对数据进行比较。 然后，如 SELECT * FROM c 的查询（其中较低（c 名称） = ' bob '）只是 SELECT * FROM c，其中 c.name = ' bob '。

## <a name="syntax"></a>语法
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr*  
   字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回字符串表达式。  
  
## <a name="examples"></a>示例
  
  下面的示例演示如何在查询中使用 `LOWER`。  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 下面是结果集：  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
