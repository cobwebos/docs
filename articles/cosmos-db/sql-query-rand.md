---
title: Azure Cosmos DB 查询语言中的 RAND
description: 了解 Azure Cosmos DB 中的 SQL 系统函数 RAND。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302213"
---
# <a name="rand-azure-cosmos-db"></a>RAND （Azure Cosmos DB）
 从 [0，1）返回随机生成的数值。
 
## <a name="syntax"></a>语法
  
```sql
RAND ()  
```  

## <a name="return-types"></a>返回类型

  返回数值表达式。

## <a name="remarks"></a>备注

  `RAND` 是非确定性函数。 重复调用 `RAND` 不会返回相同的结果。

## <a name="examples"></a>示例
  
  下面的示例返回一个随机生成的数值。
  
```sql
SELECT RAND() AS rand 
```  
  
 下面是结果集：  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
