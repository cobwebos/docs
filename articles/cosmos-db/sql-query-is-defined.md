---
title: Azure Cosmos DB 查询语言中的 IS_DEFINED
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 IS_DEFINED。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303845"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED （Azure Cosmos DB）
 返回一个布尔，它指示属性是否已经分配了值。  
  
## <a name="syntax"></a>语法
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>参数
  
*expr*  
   是任何表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个布尔表达式。  
  
## <a name="examples"></a>示例
  
  以下示例检查了指定的 JSON 文档内是否存在属性。 由于存在 "a"，第一个会返回 true，但由于不存在 "b"，第二个会返回 false。  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 下面是结果集：  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>备注

此系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中受益。

## <a name="next-steps"></a>后续步骤

- [类型检查函数 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
