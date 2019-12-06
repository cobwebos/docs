---
title: Azure Cosmos DB 查询语言中较低
description: 了解 Azure Cosmos DB 中的较低 SQL 系统函数在将大写字符数据转换为小写后返回字符串表达式
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f7cd864d90b8ab17a180a00a7a6d4bb683da9be
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873295"
---
# <a name="lower-azure-cosmos-db"></a>LOWER （Azure Cosmos DB）
 返回在将大写字符数据转换为小写后的字符串表达式。  
  
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
  
 结果集如下。  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
