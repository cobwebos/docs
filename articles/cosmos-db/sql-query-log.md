---
title: Azure Cosmos DB 查询语言中的 LOG
description: 了解 Azure Cosmos DB 中的 LOG SQL 系统函数，以便返回指定数值表达式的自然对数
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302502"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 返回指定数值表达式的自然对数。  
  
## <a name="syntax"></a>语法
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>自变量
  
*numeric_expr*  
   为数值表达式。  
  
*base*  
   设置对数底数的可选数值参数。  
  
## <a name="return-types"></a>返回类型
  
  返回数值表达式。  
  
## <a name="remarks"></a>备注
  
  默认情况下，LOG() 返回自然对数。 可以使用可选底数参数将对数的底数改为另一个值。  
  
  自然对数是底数 **e** 的对数，其中 **e** 是一个无理常数，约等于 2.718281828。  
  
  某个数的指数的自然对数就是该数本身：LOG( EXP( n ) ) = n。 并且某个数的自然对数的指数也是该数字本身：EXP( LOG( n ) ) = n。  
  
## <a name="examples"></a>示例
  
  以下示例声明一个变量并返回指定变量 (10) 的对数值。  
  
```sql
SELECT LOG(10) AS log  
```  
  
 下面是结果集：  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 以下示例计算某数指数的 `LOG`。  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 下面是结果集：  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>备注

此系统功能不会利用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure 宇宙 DB](sql-query-mathematical-functions.md)
- [系统功能 Azure 宇宙 DB](sql-query-system-functions.md)
- [Azure 宇宙 DB 简介](introduction.md)
