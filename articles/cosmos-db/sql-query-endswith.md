---
title: Azure Cosmos DB 查询语言中的 ENDSWITH
description: 了解 Azure Cosmos DB 中的 ENDSWITH SQL 系统函数，以返回一个布尔值，指示第一个字符串表达式是否以第二个结束
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299442"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH （Azure Cosmos DB）
 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。  
  
## <a name="syntax"></a>语法
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>参数
  
*str_expr1*  
   字符串表达式。  
  
*str_expr2*  
   要与*str_expr1*末尾进行比较的字符串表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  以下示例将返回以 "b" 和 "bc" 结尾的 "abc"。  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 下面是结果集：  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [字符串函数 Azure Cosmos DB](sql-query-string-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
